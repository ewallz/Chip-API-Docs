# CHIP Collect API Documentation

## Introduction

### Overview
The CHIP Collect API enables seamless integration of payment processing into your applications. This document provides an introduction to the API, including base URL, authentication requirements, and available resources to accelerate your development.

### Base URL
All API endpoints are prefixed with the following base URL:

```
https://gate.chip-in.asia/api/v1/
```

**Example Endpoint**: `POST https://gate.chip-in.asia/api/v1/purchases/`

### Authentication
Every API request requires an API key, which must be included as a Bearer token in the `Authorization` header.

**Header Format**:
```http
Authorization: Bearer <secret key>
```

#### Obtaining Your API Key
- **Location**: Generate and manage your API keys in the Developers section of your CHIP account.
- **URL**: [API Keys Management](https://portal.chip-in.asia/collect/developers/api-keys)

### Brand ID
Certain endpoints require a Brand ID, which uniquely identifies your brand within the CHIP system.

- **Location**: Find your Brand ID in the Developers section.
- **URL**: [Brand ID Details](https://portal.chip-in.asia/collect/developers/brands)

### Available Resources
To save development time, CHIP provides ready-to-go connectors, plugins, libraries, and SDKs for popular platforms and programming languages. Consider using these resources before building custom integrations.

#### Plugins
- WooCommerce
- Gravity Forms
- OpenCart
- Magento
- PrestaShop

#### Libraries
- PHP
- Java
- C#
- Node.js

#### SDKs
- iOS
- Android

### Next Steps
- Review the available plugins, libraries, and SDKs to determine if they meet your project needs.
- Obtain your API key and Brand ID from the CHIP portal.
- Explore the specific API endpoints in the detailed documentation to start integrating payment processing.

## Payment Link

### Overview
The Payment Link feature allows you to create secure, shareable links for processing payments, suitable for various use cases such as online sales, service charges, or donations.

### Example Use Cases
- Selling online products through a website
- Charging for freelance or consulting services
- Accepting donations or one-time payments

### Required API
To create a payment link, use the following API endpoint:
- **Create Purchases API**: `POST https://gate.chip-in.asia/api/v1/purchases/`

### Example JSON Payload
Below is an example of creating a payment link for a customer named Alan purchasing a mug for RM 10.00.

1. **Create a Purchase**:
   - Use the Purchases API to create a purchase.
   - Extract the `checkout_url` from the response body to use as the payment link.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Mug",
        "price": 1000
      }
    ]
  },
  "brand_id": "<<Brand_id>>"
}
```

**Note**: Replace `<<Brand_id>>` with your actual Brand ID, obtainable from [Brand ID Details](https://portal.chip-in.asia/collect/developers/brands).

## Subscriptions

### Overview
The Subscriptions feature enables recurring payments for services such as memberships, SaaS products, or educational content. This section covers creating subscriptions with free trials or registration fees, including token-based recurring charges.

### Example Use Cases
- Recurring memberships
- Software-as-a-Service (SaaS) products
- Educational content and course subscriptions

### Required APIs
- **Create Purchases API**: `POST https://gate.chip-in.asia/api/v1/purchases/`
- **Charge Token API**: `POST https://gate.chip-in.asia/api/v1/purchases/{monthly_subscription_fee_id}/charge/`

### Example Cases
The following examples demonstrate how to set up subscriptions for a customer named Alan for a gym membership, with scenarios for a free trial and a registration fee.

#### 1) Subscription with Free Trial
Alan subscribes to a gym membership with a free trial and a monthly subscription fee of RM 5.00.

1. **Create Registration Fee (Free Trial)**:
   - Use the Purchases API to create a free trial purchase.
   - Extract the `checkout_url` from the response body to use as the payment link.
   - Extract the `id` from the response body to use as a token.
   - **Note**: Alan’s card must be validated for the token to be usable.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Free Trial",
        "price": 0
      }
    ]
  },
  "skip_capture": true,
  "brand_id": "<<Brand_id>>"
}
```

**Note**: Replace `<<Brand_id>>` with your actual Brand ID, obtainable from [Brand ID Details](https://portal.chip-in.asia/collect/developers/brands).

2. **Create Monthly Subscription**:
   - Use the Purchases API to create a subscription purchase.
   - Extract the `id` from the response body for use in the Charge Token API.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Subscription fee",
        "price": 500
      }
    ]
  },
  "brand_id": "<<Brand_id>>"
}
```

3. **Charge Subscription Fee**:
   - Use the Charge Token API to charge the subscription fee using the free trial token.
   - **Endpoint**: `POST https://gate.chip-in.asia/api/v1/purchases/{monthly_subscription_fee_id}/charge/`

**Example Payload**:
```json
{
  "recurring_token": "<<free_trial_id>>"
}
```

**Note**: Replace `<<free_trial_id>>` with the `id` from the free trial purchase response.

#### 2) Subscription with Registration Fee
Alan subscribes to a gym membership with a registration fee of RM 20.00 and a monthly subscription fee of RM 5.00.

1. **Create Registration Fee**:
   - Use the Purchases API to create a registration fee purchase.
   - Extract the `checkout_url` from the response body to use as the payment link.
   - Extract the `id` from the response body to use as a token.
   - **Note**: Alan’s registration fee must be paid for the token to be usable.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Registration Fee",
        "price": 2000
      }
    ]
  },
  "payment_method_whitelist": ["visa", "mastercard", "maestro"],
  "force_recurring": true,
  "brand_id": "<<Brand_id>>"
}
```

**Note**: Replace `<<Brand_id>>` with your actual Brand ID, obtainable from [Brand ID Details](https://portal.chip-in.asia/collect/developers/brands).

2. **Create Monthly Subscription**:
   - Use the Purchases API to create a subscription purchase.
   - Extract the `id` from the response body for use in the Charge Token API.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Subscription fee",
        "price": 500
      }
    ]
  },
  "brand_id": "<<Brand_id>>"
}
```

3. **Charge Subscription Fee**:
   - Use the Charge Token API to charge the subscription fee using the registration fee token.
   - **Endpoint**: `POST https://gate.chip-in.asia/api/v1/purchases/{monthly_subscription_fee_id}/charge/`

**Example Payload**:
```json
{
  "recurring_token": "<<registration_fee_id>>"
}
```

**Note**: Replace `<<registration_fee_id>>` with the `id` from the registration fee purchase response.

### FAQ
#### Recurring Token FAQs
1. **What is the token tied to?**
   - The token is tied to the `brand_id`.
2. **How is the token referenced?**
   - The token uses `customer_email` as a reference.
3. **Where can I see my customers' tokens?**
   - Use the **List Token API** to view tokens per customer.
4. **How do I delete a token?**
   - Use the **Delete Token API** to remove a token.
   - **Note**: Destroying the token does not refund the customer for any paid registration fee.

## Pre-Authorization

### Overview
The Pre-Authorization feature allows you to authorize a payment amount without immediately capturing the funds, useful for scenarios like reservations or rentals where funds are held until a later action (e.g., capturing or releasing).

### Example Use Cases
- Room reservations
- Rental services
- Pay-per-use parking

### Required APIs
- **Create Purchases API**: `POST https://gate.chip-in.asia/api/v1/purchases/`
- **Capture Payment API**: `POST https://gate.chip-in.asia/api/v1/purchases/{purchase_id}/capture/`

### Example JSON Payload
Below is an example of setting up a pre-authorization for a customer named Alan renting a hotel room for RM 200.00 with an additional security deposit of RM 100.00.

1. **Create a Reservation Fee**:
   - Use the Purchases API to create a reservation purchase.
   - Extract the `checkout_url` from the response body to use as the payment link.
   - Extract the `id` from the response body to use as a token.
   - **Note**: Alan’s reservation fee must be paid for the token to be usable.

**Example Payload**:
```json
{
  "client": {
    "email": "test@test.com",
    "full_name": "Alan"
  },
  "purchase": {
    "products": [
      {
        "name": "Room fee",
        "price": 20000
      },
      {
        "name": "Security deposit",
        "price": 10000
      }
    ]
  },
  "skip_capture": true,
  "brand_id": "<<Brand_id>>"
}
```

**Note**: Replace `<<Brand_id>>` with your actual Brand ID, obtainable from [Brand ID Details](https://portal.chip-in.asia/collect/developers/brands).

2. **Charge the Funds**:
   - Use the Capture Payment API to capture the authorized funds.
   - **Endpoint**: `POST https://gate.chip-in.asia/api/v1/purchases/{purchase_id}/capture/`

   - **Full Capture (including security deposit)**:
```json
{
  "amount": 30000
}
```

   - **Partial Capture (only room fee)**:
```json
{
  "amount": 20000
}
```

   **Note**: Partial capture is supported, and any uncharged funds will be automatically released.

### Skip Capture Parameter
When the `skip_capture` parameter is set to `true` in the Purchases API, the payment is authorized without capturing the funds, holding the amount in the customer’s account without withdrawing it.

## Callbacks

### Overview
Callbacks allow asynchronous notifications for events like successful purchases or other system activities. Two methods are supported: **Purchase Success Callbacks** and **Webhooks**.

### Purchase Success Callbacks
- **Definition**: Specify a target URL in the `success_callback` field when creating a purchase via the **Create Purchases API** (`POST https://gate.chip-in.asia/api/v1/purchases/`).
- **Behavior**: A callback is triggered when specific events occur (e.g., purchase completion). The payload is a JSON-encoded `Purchase` object, representing a snapshot of the purchase state at the time of the event.
- **Payload Details**:
  - Includes an `event_type` field indicating the specific event (refer to the Event schema for details).
  - Signed using a company-wide key pair.
- **Public Key**: Obtain the public key for signature verification via `GET https://gate.chip-in.asia/api/v1/public_key/`.
- **Reference**: See the [Authentication](#authentication) section for signature verification details.

### Webhooks
- **Management**: Create and modify webhooks using the [Webhook CRUD API](https://docs.chip-in.asia/chip-collect/webhook-crud).
- **Payload**: Webhook callback payloads are signed using a dedicated key pair.
- **Public Key**: Obtain the public key for webhook authentication from the `Webhook.public_key` field.
- **Reference**: See the [Authentication](#authentication) section for signature verification details.

### Delivery Protocol
- **Retry Mechanism**: If a callback delivery fails (i.e., does not receive a 200-series HTTP response code), the system retries up to 8 times at exponentially increasing intervals.
- **Timeout**: No further attempts are made if the callback is not delivered within 36 hours of triggering.
- **Duplicate Delivery**: Due to network issues, a target server may receive a callback multiple times, even after responding with a 200-series status code.
- **Sequential Delivery**: Callbacks are delivered sequentially based on the events triggered. For example, `purchase.paid` callbacks are not sent until all `purchase.created` callbacks for the same purchase are successfully delivered.

## Authentication

### Overview
Callback payloads are signed using asymmetric (public-key) cryptography to ensure authenticity. Each callback includes an `X-Signature` header for verification.

### Signature Details
- **Header**: `X-Signature` contains a base64-encoded RSA PKCS#1 v1.5 signature of the SHA256 digest of the request body.
- **Public Keys**:
  - For **Purchase Success Callbacks**: Obtain the public key via `GET https://gate.chip-in.asia/api/v1/public_key/`.
  - For **Webhooks**: Obtain the public key from the `Webhook.public_key` field of the corresponding webhook.
- **Verification**: Verify the signature to ensure the callback's authenticity.
- **Note**: The provider is not responsible for financial losses due to unverified payload signatures.

## What We Offer

### Overview
The CHIP Collect API provides features to enhance payment processing, customer experience, and integration flexibility.

### Features
1. **Purchase Notifications**:
   - Receive real-time notifications via system callbacks or email when a transaction is successfully completed.
2. **Send Receipts to Customers**:
   - Automatically send standardized receipts to customers on your behalf.
3. **Redirect Customer After Purchase**:
   - Automatically redirect customers to a designated webpage based on the transaction outcome (success, failure, or cancellation).
4. **Secure Customer Card Information**:
   - Tokenize and securely store customer card details for future purchases or subscriptions.
   - **Configuration**: Set `skip_capture: true` and `price: 0` in the Purchases API.
5. **Pre-Authorize Payment**:
   - Authorize and hold an amount in the customer’s account for later capture or release.
   - **Configuration**: Set `skip_capture: true` and `price: <<amount higher than 0>>` in the Purchases API.
6. **Free Form API Field**:
   - Store custom values (e.g., invoice numbers or reference data) to meet specific business needs.
7. **Payment Method Whitelist**:
   - Enable or disable specific payment methods to suit your business requirements.
   - **Available Payment Methods**:
     - FPX Online Banking
     - Local credit and debit cards
     - Foreign credit and debit cards
     - E-wallets
     - DuitNow QR (Online)
     - Buy Now, Pay Later
     - On-Premise POS Terminal

## FPX

### Overview
The FPX (Financial Process Exchange) payment option allows you to redirect customers to a specific bank’s payment page for seamless online banking transactions. This section explains how to configure the `checkout_url` to automatically redirect customers to a chosen bank.

### Instructions
When creating a purchase via the **Create Purchases API** (`POST https://gate.chip-in.asia/api/v1/purchases/`), you receive a `checkout_url` to redirect the buyer to the payment page. To automatically redirect customers to a specific bank, append the following query parameters to the `checkout_url`:

```
?preferred=<FPX>&fpx_bank_code=<Bank Code>
```

- **`<FPX>`**: Specifies the FPX transaction type (B2C or B2B1).
- **`<Bank Code>`**: Identifies the specific bank for redirection.

### FPX Type List
The available options for the `<FPX>` parameter are:

| Type         | Value          |
|--------------|----------------|
| FPX B2C      | fpx            |
| FPX B2B1     | fpx_b2b1       |

### Bank Code List
The available options for the `<Bank Code>` parameter are:

| Bank Name                | Value (Bank Code) |
|--------------------------|-------------------|
| Affin Bank               | ABB0233           |
| Alliance Bank (Personal) | ABMB0212          |
| AGRONet                  | AGRO01            |
| AmBank                   | AMBB0209          |
| Bank Islam               | BIMB0340          |
| Bank Muamalat            | BMMB0341          |
| Bank Rakyat              | BKRM0602          |
| Bank Of China            | BOCM01            |
| BSN                      | BSN0601           |
| CIMB Bank                | BCBB0235          |
| Hong Leong Bank          | HLB0224           |
| HSBC Bank                | HSBC0223          |
| KFH                      | KFH0346           |
| Maybank2E                | MBB0228           |
| Maybank2u                | MB2U0227          |
| OCBC Bank                | OCBC0229          |
| Public Bank              | PBB0233           |
| RHB Bank                 | RHB0218           |
| Standard Chartered       | SCB0216           |
| UOB Bank                 | UOB0226           |

### Examples
To redirect a customer to a specific bank, append the parameters to the `checkout_url`. For instance:

- For FPX B2C with Maybank2u:
  ```
  <checkout_url>?preferred=fpx&fpx_bank_code=MB2U0227
  ```

- For FPX B2B1 with CIMB Bank:
  ```
  <checkout_url>?preferred=fpx_b2b1&fpx_bank_code=BCBB0235
  ```

Replace `<checkout_url>` with the actual URL received from the Purchases API response.

### Real-World Scenario
In practice, after creating a purchase:
1. Receive the `checkout_url` from the Purchases API response.
2. Append the appropriate `preferred` and `fpx_bank_code` parameters.
3. Redirect the customer to the modified URL to complete the payment via the selected bank’s online banking portal.

## Card (VISA/Mastercard)

### Overview
The Card payment option allows customers to pay using VISA or Mastercard via a direct form submission. This section explains how to use the `direct_post_url` provided after creating a purchase to submit card details.

### Instructions
When creating a purchase via the **Create Purchases API** (`POST https://gate.chip-in.asia/api/v1/purchases/`), you receive a `direct_post_url`. Use this URL to submit an HTML form POST containing the necessary card data.

### Example Code
Below is an example HTML form for submitting card details:

```html
<form method="POST" action="https://payments.chip-in.asia/p/3943e0d8-dfc0-4fb5-ae5f-4872f6d0990d/">
    <input type="text" name="cardholder_name" value="">
    <input type="text" name="card_number" value="">
    <input type="text" name="expires" value="">
    <input type="text" name="cvc" value="">
    <input type="submit">
</form>
```

**Note**: Replace the `action` URL with the actual `direct_post_url` received from the Purchases API response.

### Real-World Scenario
*Note*: The source documentation references a "Real-World scenario" but does not provide specific details. In practice:
1. Create a purchase using the Purchases API.
2. Receive the `direct_post_url` from the response.
3. Render an HTML form with fields for `cardholder_name`, `card_number`, `expires`, and `cvc`.
4. Submit the form to the `direct_post_url` to process the payment.

### Testing Integration
You can test card payments using test card details. Refer to the [Testing Integration](#testing-integration) section under [What We Offer](#what-we-offer) for details on simulating successful and failed payments.

## E-Wallet

### Overview
The E-Wallet payment option allows customers to pay using supported e-wallets by redirecting them to the appropriate payment page. This section explains how to configure the `checkout_url` to automatically navigate to a specific e-wallet.

### Instructions
When creating a purchase via the **Create Purchases API** (`POST https://gate.chip-in.asia/api/v1/purchases/`), you receive a `checkout_url`. To automatically redirect customers to a specific e-wallet, append the following query parameters to the `checkout_url`:

```
?preferred=<Wallet>&razer_bank_code=<Wallet Code>
```

- **`<Wallet>`**: Specifies the e-wallet provider.
- **`<Wallet Code>`**: Identifies the specific e-wallet for redirection.

### E-Wallet List
The available options for `<Wallet>` and `<Wallet Code>` are:

| E-Wallet Name        | Value (Wallet)       | Value (Wallet Code)   |
|----------------------|---------------------|-----------------------|
| GrabPay              | razer_grabpay       | GrabPay               |
| Touch ‘n Go eWallet  | razer_tng           | TNG-EWALLET           |
| ShopeePay            | razer_shopeepay     | ShopeePay             |
| Maybank QR           | razer_maybankqr     | MB2U_QRPay-Push       |

### Examples
To redirect a customer to a specific e-wallet, append the parameters to the `checkout_url`. For instance:

- For GrabPay:
  ```
  <checkout_url>?preferred=razer_grabpay&razer_bank_code=GrabPay
  ```

- For Touch ‘n Go eWallet:
  ```
  <checkout_url>?preferred=razer_tng&razer_bank_code=TNG-EWALLET
  ```

Replace `<checkout_url>` with the actual URL received from the Purchases API response.

### Real-World Scenario
In practice:
1. Create a purchase using the Purchases API.
2. Receive the `checkout_url` from the response.
3. Append the appropriate `preferred` and `razer_bank_code` parameters.
4. Redirect the customer to the modified URL to complete the payment via the selected e-wallet.

### Testing Integration
You can test e-wallet payments using test purchases. Refer to the [Testing Integration](#testing-integration) section under [What We Offer](#what-we-offer) for details on simulating successful and failed payments, where applicable.

## Duitnow-QR

### Overview
The Duitnow-QR payment option allows customers to pay by scanning a QR code via supported apps. This section explains how to configure the `checkout_url` to redirect customers to the Duitnow QR payment page.

### Instructions
When creating a purchase via the **Create Purchases API** (`POST https://gate.chip-in.asia/api/v1/purchases/`), you receive a `checkout_url`. To automatically redirect customers to the Duitnow QR payment page, append the following query parameter to the `checkout_url`:

```
?preferred=duitnow_qr
```

### Examples
To redirect a customer to the Duitnow QR payment page:
```
<checkout_url>?preferred=duitnow_qr
```

Replace `<checkout_url>` with the actual URL received from the Purchases API response.

### Real-World Scenario
In practice:
1. Create a purchase using the Purchases API.
2. Receive the `checkout_url` from the response.
3. Append the `preferred=duitnow_qr` parameter.
4. Redirect the customer to the modified URL to scan the QR code and complete the payment.

### Testing Integration
You can test Duitnow QR payments using test purchases. Refer to the [Testing Integration](#testing-integration) section under [What We Offer](#what-we-offer) for details on simulating successful and failed payments, where applicable.

## Testing Integration
You can test payment-related functionalities (e.g., Payment Link, Subscriptions, Pre-Authorization, Card, E-Wallet, Duitnow-QR) using test purchases. The following test card details are provided for simulating successful and failed payments, where applicable.

### Test Cards for Successful Payments
- **Non-3D Secure Card**: `4444 3333 2222 1111`
- **3D Secure Card**: `5555 5555 5555 4444`

**Card Details**:
- **Cardholder Name**: Any name
- **Expiry Date**: Any date greater than or equal to the current month/year
- **CVC**: `123`

### Testing Failed Payments
To simulate a failed payment:
- Use an **incorrect CVC** or **expired date**.
- For a **3D Secure card** in Server-to-Server (S2S) checkout, an incorrect CVC will trigger an authorization failure after the customer returns from the test Access Control Server (ACS).
- An incorrect expiry date will result in an immediate data validation error before the ACS step.
