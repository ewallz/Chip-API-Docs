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


# CHIP Purchase API Documentation

## Charge a Purchase Using a Saved Token

This endpoint allows you to charge a purchase using a `recurring_token` provided in the request body. The `recurring_token` should be the `id` of a Purchase where `is_recurring_token` is set to `true`. The purchase will be paid using the same payment method (e.g., the same card) as the one used for the `recurring_token` purchase.

If the operation takes too long to process on the acquirer’s side, you will receive a response with HTTP status code `200` and a Purchase object with `status` set to `pending_charge`. A corresponding webhook callback for the `purchase.pending_charge` event will also be sent. To be notified of a successful operation completion, subscribe to the `purchase.paid` callback event, which delivers an updated Purchase with `status` set to `paid`. If the charge fails, you will receive a `purchase.payment_failure` callback event. In case of a recurring charge failure due to a payment processing error, an HTTP response code `400` with error code `purchase_charge_error` will be returned. To get more details about the error, perform a `GET /purchases/` request for the Purchase you tried to charge. The `transaction_data.attempts[]` array (newest element first) will contain the corresponding attempt with error code and description in the `.error` parameter.

### Endpoint

**POST** `/purchases/{id}/charge/`

### Authorizations

| Header | Type | Description | Required |
|--------|------|-------------|----------|
| Authorization | string | Bearer authentication header of the form `Bearer <token>`, where `<token>` is your auth token. | Yes |

### Path Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| id | string<uuid> | Object ID (UUID) of the purchase to charge. | Yes |

### Request Body

**Content-Type:** `application/json`

| Field | Type | Description | Required |
|-------|------|-------------|----------|
| recurring_token | string<uuid> | The `id` of a Purchase where `is_recurring_token` is `true`. | Yes |

#### Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/{id}/charge/ \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "recurring_token": "ea582899-78ec-4c3a-9cb3-08f922e556b6"
  }'
```

### Response

**Status Code:** `200 OK`

**Content-Type:** `application/json`

The response contains a record of a single purchase operation, which could originate from an e-commerce integration or an invoice sent. The Purchase object includes a `status` attribute, which can be `created`, `paid`, `refunded`, or other states.

#### Response Body

| Field | Type | Description |
|-------|------|-------------|
| type | string | Type of the purchase. |
| id | string<uuid> | Unique identifier of the purchase. |
| created_on | integer | Timestamp when the purchase was created (Unix epoch). |
| updated_on | integer | Timestamp when the purchase was last updated (Unix epoch). |
| client | object | Details about the client. |
| client.bank_account | string | Client's bank account number. |
| client.bank_code | string | Client's bank code. |
| client.email | string | Client's email address (e.g., `jsmith@example.com`). |
| client.phone | string | Client's phone number (e.g., `+44 45643564564`). |
| client.full_name | string | Client's full name. |
| client.personal_code | string | Client's personal code. |
| client.street_address | string | Client's street address. |
| client.country | string | Client's country. |
| client.city | string | Client's city. |
| client.zip_code | string | Client's zip code. |
| client.state | string | Client's state. |
| client.shipping_street_address | string | Client's shipping street address. |
| client.shipping_country | string | Client's shipping country. |
| client.shipping_city | string | Client's shipping city. |
| client.shipping_zip_code | string | Client's shipping zip code. |
| client.shipping_state | string | Client's shipping state. |
| client.cc | array<string> | List of email addresses to CC (e.g., `["jsmith@example.com"]`). |
| client.bcc | array<string> | List of email addresses to BCC (e.g., `["jsmith@example.com"]`). |
| client.legal_name | string | Client's legal name. |
| client.brand_name | string | Client's brand name. |
| client.registration_number | string | Client's registration number. |
| client.tax_number | string | Client's tax number. |
| purchase | object | Details about the purchase. |
| purchase.currency | string | Currency of the purchase. |
| purchase.products | array<object> | List of products in the purchase. |
| purchase.products[].name | string | Product name. |
| purchase.products[].quantity | integer | Product quantity (e.g., `1`). |
| purchase.products[].price | integer | Product price (e.g., `1`). |
| purchase.products[].discount | integer | Product discount (e.g., `1`). |
| purchase.products[].tax_percent | integer | Tax percentage for the product (e.g., `0`). |
| purchase.products[].category | string | Product category. |
| purchase.total | integer | Total amount of the purchase (e.g., `123`). |
| purchase.language | string | Language of the purchase. Default value is controlled in the Company -> Brand section of the merchant portal (default is `en`). |
| purchase.notes | string | Additional notes for the purchase. |
| purchase.debt | integer | Debt amount (e.g., `123`). |
| purchase.subtotal_override | integer | Override for the subtotal (e.g., `123`). |
| purchase.total_tax_override | integer | Override for the total tax (e.g., `123`). |
| purchase.total_discount_override | integer | Override for the total discount (e.g., `123`). |
| purchase.total_override | integer | Override for the total amount (e.g., `123`). |
| purchase.request_client_details | array | List of client details to request (empty by default). |
| purchase.timezone | string | Timezone of the purchase (e.g., `Europe/Oslo`). |
| purchase.due_strict | boolean | Whether the due date is strict (e.g., `false`). |
| purchase.email_message | string | Custom email message for the purchase. |
| payment | object | Payment details. |
| payment.is_outgoing | boolean | Whether the payment is outgoing (e.g., `false`). |
| payment.payment_type | string | Type of payment (e.g., `purchase`). |
| payment.amount | integer | Payment amount (e.g., `123`). |
| payment.currency | string | Payment currency. |
| payment.net_amount | integer | Net payment amount (e.g., `123`). |
| payment.fee_amount | integer | Fee amount (e.g., `123`). |
| payment.pending_amount | integer | Pending payment amount (e.g., `123`). |
| payment.pending_unfreeze_on | integer | Timestamp when pending amount unfreezes (Unix epoch, e.g., `1619740800`). |
| payment.description | string | Payment description. |
| payment.paid_on | integer | Timestamp when payment was made (Unix epoch, e.g., `1619740800`). |
| payment.remote_paid_on | integer | Timestamp when payment was processed remotely (Unix epoch, e.g., `1619740800`). |
| issuer_details | object | Details about the issuer. |
| issuer_details.website | string | Issuer's website. |
| issuer_details.legal_street_address | string | Issuer's legal street address. |
| issuer_details.legal_country | string | Issuer's legal country. |
| issuer_details.legal_city | string | Issuer's legal city. |
| issuer_details.legal_zip_code | string | Issuer's legal zip code. |
| issuer_details.bank_accounts | array<object> | List of issuer's bank accounts. |
| issuer_details.bank_accounts[].bank_account | string | Bank account number. |
| issuer_details.bank_accounts[].bank_code | string | Bank code. |
| issuer_details.legal_name | string | Issuer's legal name. |
| issuer_details.brand_name | string | Issuer's brand name. |
| issuer_details.registration_number | string | Issuer's registration number. |
| issuer_details.tax_number | string | Issuer's tax number. |
| transaction_data | object | Transaction details. |
| transaction_data.payment_method | string | Payment method used. |
| transaction_data.extra | object | Additional transaction data (empty object by default). |
| transaction_data.country | string | Country of the transaction. |
| transaction_data.attempts | array<object> | List of transaction attempts. |
| transaction_data.attempts[].type | string | Type of attempt (e.g., `execute`). |
| transaction_data.attempts[].successful | boolean | Whether the attempt was successful (e.g., `true`). |
| transaction_data.attempts[].payment_method | string | Payment method for the attempt. |
| transaction_data.attempts[].extra | object | Additional data for the attempt (empty object by default). |
| transaction_data.attempts[].country | string | Country of the attempt. |
| transaction_data.attempts[].client_ip | string | Client's IP address. |
| transaction_data.attempts[].processing_time | integer | Processing time of the attempt (Unix epoch, e.g., `1619740800`). |
| transaction_data.attempts[].error | object | Error details for the attempt. |
| transaction_data.attempts[].error.code | string | Error code. |
| transaction_data.attempts[].error.message | string | Error message. |
| status | string | Current status of the purchase (e.g., `created`, `pending_charge`, `paid`, `refunded`). |
| status_history | array<object> | History of status changes. |
| status_history[].status | string | Status at a specific point (e.g., `created`). |
| status_history[].timestamp | integer | Timestamp of the status change (Unix epoch, e.g., `1619740800`). |
| status_history[].related_object | object | Related object for the status change. |
| status_history[].related_object.type | string | Type of the related object. |
| status_history[].related_object.id | string<uuid> | ID of the related object (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| viewed_on | integer | Timestamp when the purchase was viewed (Unix epoch, e.g., `1619740800`). |
| company_id | string<uuid> | ID of the company (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| is_test | boolean | Whether the purchase is in test mode (e.g., `true`). |
| user_id | string<uuid> | ID of the user (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| brand_id | string<uuid> | ID of the brand (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| billing_template_id | string<uuid> | ID of the billing template (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| client_id | string<uuid> | ID of the client (can be `null`). |
| send_receipt | boolean | Whether to send a receipt (e.g., `false`). |
| is_recurring_token | boolean | Whether the purchase is a recurring token (e.g., `true`). |
| recurring_token | string<uuid> | ID of the recurring token (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| skip_capture | boolean | Whether to skip capture (e.g., `false`). |
| force_recurring | boolean | Whether to force recurring payment (e.g., `false`). |
| reference_generated | string | Generated reference for the purchase. |
| reference | string | Reference for the purchase. |
| notes | string | Additional notes for the purchase. |
| issued | string | Date the purchase was issued (e.g., `2020-04-30`). |
| due | integer | Due date of the purchase (Unix epoch, e.g., `1619740800`). |
| refund_availability | string | Refund availability (e.g., `all`). |
| refundable_amount | integer | Amount available for refund (e.g., `123`). |
| currency_conversion | object | Currency conversion details. |
| currency_conversion.original_currency | string | Original currency of the purchase. |
| currency_conversion.original_amount | integer | Original amount before conversion (e.g., `123`). |
| currency_conversion.exchange_rate | integer | Exchange rate used (e.g., `123`). |
| payment_method_whitelist | array<string> | List of allowed payment methods (e.g., `["<string>"]`). |
| success_redirect | string | URL for successful payment redirect. |
| failure_redirect | string | URL for failed payment redirect. |
| cancel_redirect | string | URL for canceled payment redirect. |
| success_callback | string | URL for success callback. |
| creator_agent | string | Agent that created the purchase. |
| platform | string | Platform used (e.g., `web`). |
| product | string | Product type (e.g., `purchases`). |
| created_from_ip | string | IP address from which the purchase was created. |
| invoice_url | string | URL of the invoice. |
| checkout_url | string | URL for checkout. |
| direct_post_url | string | URL for direct post. |
| marked_as_paid | boolean | Whether the purchase is marked as paid (e.g., `true`). |
| order_id | string | Order ID for the purchase. |

#### Example Response

```json
{
  "type": "<string>",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "<string>",
    "bank_code": "<string>",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "<string>",
    "personal_code": "<string>",
    "street_address": "<string>",
    "country": "<string>",
    "city": "<string>",
    "zip_code": "<string>",
    "state": "<string>",
    "shipping_street_address": "<string>",
    "shipping_country": "<string>",
    "shipping_city": "<string>",
    "shipping_zip_code": "<string>",
    "shipping_state": "<string>",
    "cc": [
      "jsmith@example.com"
    ],
    "bcc": [
      "jsmith@example.com"
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "purchase": {
    "currency": "<string>",
    "products": [
      {
        "name": "<string>",
        "quantity": 1,
        "price": 1,
        "discount": 1,
        "tax_percent": 0,
        "category": "<string>"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "<string>",
    "debt": 123,
    "subtotal_override": 123,
    "total_tax_override": 123,
    "total_discount_override": 123,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "<string>"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "<string>",
    "net_amount": 123,
    "fee_amount": 123,
    "pending_amount": 123,
    "pending_unfreeze_on": 1619740800,
    "description": "<string>",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "<string>",
    "legal_street_address": "<string>",
    "legal_country": "<string>",
    "legal_city": "<string>",
    "legal_zip_code": "<string>",
    "bank_accounts": [
      {
        "bank_account": "<string>",
        "bank_code": "<string>"
      }
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "transaction_data": {
    "payment_method": "<string>",
    "extra": {},
    "country": "<string>",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "<string>",
        "extra": {},
        "country": "<string>",
        "client_ip": "<string>",
        "processing_time": 1619740800,
        "error": {
          "code": "<string>",
          "message": "<string>"
        }
      }
    ]
  },
  "status": "created",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "<string>",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "<string>",
  "reference": "<string>",
  "notes": "<string>",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "<string>",
    "original_amount": 123,
    "exchange_rate": 123
  },
  "payment_method_whitelist": [
    "<string>"
  ],
  "success_redirect": "<string>",
  "failure_redirect": "<string>",
  "cancel_redirect": "<string>",
  "success_callback": "<string>",
  "creator_agent": "<string>",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "<string>",
  "invoice_url": "<string>",
  "checkout_url": "<string>",
  "direct_post_url": "<string>",
  "marked_as_paid": true,
  "order_id": "<string>"
}
```

## Delete a Recurring Token Associated with a Purchase

This endpoint allows you to delete a recurring token associated with a purchase. Upon successful execution, the `is_recurring_token` field of the purchase will be set to `false`, and the purchase’s ID can no longer be used as a `recurring_token`. Additionally, any associated `ClientRecurringToken` will be deleted.

If the operation takes too long to process on the acquirer’s side, you will receive a response with HTTP status code `200` and a corresponding webhook callback for the `purchase.pending_recurring_token_delete` event. To be notified of a successful operation completion, subscribe to the `purchase.recurring_token_deleted` callback event.

### Endpoint

**POST** `/purchases/{id}/delete_recurring_token/`

### Authorizations

| Header | Type | Description | Required |
|--------|------|-------------|----------|
| Authorization | string | Bearer authentication header of the form `Bearer <token>`, where `<token>` is your auth token. | Yes |

### Path Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| id | string<uuid> | Object ID (UUID) of the purchase whose recurring token is to be deleted. | Yes |

### Request Body

No request body is required for this endpoint.

#### Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/{id}/delete_recurring_token/ \
  --header 'Authorization: Bearer <token>'
```

### Response

**Status Code:** `200 OK`

**Content-Type:** `application/json`

The response contains a record of a single purchase operation, which could originate from an e-commerce integration or an invoice sent. The Purchase object includes a `status` attribute, which can be `created`, `paid`, `refunded`, or other states. After this operation, the `is_recurring_token` field will be set to `false`.

#### Response Body

| Field | Type | Description |
|-------|------|-------------|
| type | string | Type of the purchase. |
| id | string<uuid> | Unique identifier of the purchase. |
| created_on | integer | Timestamp when the purchase was created (Unix epoch). |
| updated_on | integer | Timestamp when the purchase was last updated (Unix epoch). |
| client | object | Details about the client. |
| client.bank_account | string | Client's bank account number. |
| client.bank_code | string | Client's bank code. |
| client.email | string | Client's email address (e.g., `jsmith@example.com`). |
| client.phone | string | Client's phone number (e.g., `+44 45643564564`). |
| client.full_name | string | Client's full name. |
| client.personal_code | string | Client's personal code. |
| client.street_address | string | Client's street address. |
| client.country | string | Client's country. |
| client.city | string | Client's city. |
| client.zip_code | string | Client's zip code. |
| client.state | string | Client's state. |
| client.shipping_street_address | string | Client's shipping street address. |
| client.shipping_country | string | Client's shipping country. |
| client.shipping_city | string | Client's shipping city. |
| client.shipping_zip_code | string | Client's shipping zip code. |
| client.shipping_state | string | Client's shipping state. |
| client.cc | array<string> | List of email addresses to CC (e.g., `["jsmith@example.com"]`). |
| client.bcc | array<string> | List of email addresses to BCC (e.g., `["jsmith@example.com"]`). |
| client.legal_name | string | Client's legal name. |
| client.brand_name | string | Client's brand name. |
| client.registration_number | string | Client's registration number. |
| client.tax_number | string | Client's tax number. |
| purchase | object | Details about the purchase. |
| purchase.currency | string | Currency of the purchase. |
| purchase.products | array<object> | List of products in the purchase. |
| purchase.products[].name | string | Product name. |
| purchase.products[].quantity | integer | Product quantity (e.g., `1`). |
| purchase.products[].price | integer | Product price (e.g., `1`). |
| purchase.products[].discount | integer | Product discount (e.g., `1`). |
| purchase.products[].tax_percent | integer | Tax percentage for the product (e.g., `0`). |
| purchase.products[].category | string | Product category. |
| purchase.total | integer | Total amount of the purchase (e.g., `123`). |
| purchase.language | string | Language of the purchase. Default value is controlled in the Company -> Brand section of the merchant portal (default is `en`). |
| purchase.notes | string | Additional notes for the purchase. |
| purchase.debt | integer | Debt amount (e.g., `123`). |
| purchase.subtotal_override | integer | Override for the subtotal (e.g., `123`). |
| purchase.total_tax_override | integer | Override for the total tax (e.g., `123`). |
| purchase.total_discount_override | integer | Override for the total discount (e.g., `123`). |
| purchase.total_override | integer | Override for the total amount (e.g., `123`). |
| purchase.request_client_details | array | List of client details to request (empty by default). |
| purchase.timezone | string | Timezone of the purchase (e.g., `Europe/Oslo`). |
| purchase.due_strict | boolean | Whether the due date is strict (e.g., `false`). |
| purchase.email_message | string | Custom email message for the purchase. |
| payment | object | Payment details. |
| payment.is_outgoing | boolean | Whether the payment is outgoing (e.g., `false`). |
| payment.payment_type | string | Type of payment (e.g., `purchase`). |
| payment.amount | integer | Payment amount (e.g., `123`). |
| payment.currency | string | Payment currency. |
| payment.net_amount | integer | Net payment amount (e.g., `123`). |
| payment.fee_amount | integer | Fee amount (e.g., `123`). |
| payment.pending_amount | integer | Pending payment amount (e.g., `123`). |
| payment.pending_unfreeze_on | integer | Timestamp when pending amount unfreezes (Unix epoch, e.g., `1619740800`). |
| payment.description | string | Payment description. |
| payment.paid_on | integer | Timestamp when payment was made (Unix epoch, e.g., `1619740800`). |
| payment.remote_paid_on | integer | Timestamp when payment was processed remotely (Unix epoch, e.g., `1619740800`). |
| issuer_details | object | Details about the issuer. |
| issuer_details.website | string | Issuer's website. |
| issuer_details.legal_street_address | string | Issuer's legal street address. |
| issuer_details.legal_country | string | Issuer's legal country. |
| issuer_details.legal_city | string | Issuer's legal city. |
| issuer_details.legal_zip_code | string | Issuer's legal zip code. |
| issuer_details.bank_accounts | array<object> | List of issuer's bank accounts. |
| issuer_details.bank_accounts[].bank_account | string | Bank account number. |
| issuer_details.bank_accounts[].bank_code | string | Bank code. |
| issuer_details.legal_name | string | Issuer's legal name. |
| issuer_details.brand_name | string | Issuer's brand name. |
| issuer_details.registration_number | string | Issuer's registration number. |
| issuer_details.tax_number | string | Issuer's tax number. |
| transaction_data | object | Transaction details. |
| transaction_data.payment_method | string | Payment method used. |
| transaction_data.extra | object | Additional transaction data (empty object by default). |
| transaction_data.country | string | Country of the transaction. |
| transaction_data.attempts | array<object> | List of transaction attempts. |
| transaction_data.attempts[].type | string | Type of attempt (e.g., `execute`). |
| transaction_data.attempts[].successful | boolean | Whether the attempt was successful (e.g., `true`). |
| transaction_data.attempts[].payment_method | string | Payment method for the attempt. |
| transaction_data.attempts[].extra | object | Additional data for the attempt (empty object by default). |
| transaction_data.attempts[].country | string | Country of the attempt. |
| transaction_data.attempts[].client_ip | string | Client's IP address. |
| transaction_data.attempts[].processing_time | integer | Processing time of the attempt (Unix epoch, e.g., `1619740800`). |
| transaction_data.attempts[].error | object | Error details for the attempt. |
| transaction_data.attempts[].error.code | string | Error code. |
| transaction_data.attempts[].error.message | string | Error message. |
| status | string | Current status of the purchase (e.g., `created`, `paid`, `refunded`). |
| status_history | array<object> | History of status changes. |
| status_history[].status | string | Status at a specific point (e.g., `created`). |
| status_history[].timestamp | integer | Timestamp of the status change (Unix epoch, e.g., `1619740800`). |
| status_history[].related_object | object | Related object for the status change. |
| status_history[].related_object.type | string | Type of the related object. |
| status_history[].related_object.id | string<uuid> | ID of the related object (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| viewed_on | integer | Timestamp when the purchase was viewed (Unix epoch, e.g., `1619740800`). |
| company_id | string<uuid> | ID of the company (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| is_test | boolean | Whether the purchase is in test mode (e.g., `true`). |
| user_id | string<uuid> | ID of the user (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| brand_id | string<uuid> | ID of the brand (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| billing_template_id | string<uuid> | ID of the billing template (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| client_id | string<uuid> | ID of the client (can be `null`). |
| send_receipt | boolean | Whether to send a receipt (e.g., `false`). |
| is_recurring_token | boolean | Whether the purchase is a recurring token (set to `false` after this operation). |
| recurring_token | string<uuid> | ID of the recurring token (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| skip_capture | boolean | Whether to skip capture (e.g., `false`). |
| force_recurring | boolean | Whether to force recurring payment (e.g., `false`). |
| reference_generated | string | Generated reference for the purchase. |
| reference | string | Reference for the purchase. |
| notes | string | Additional notes for the purchase. |
| issued | string | Date the purchase was issued (e.g., `2020-04-30`). |
| due | integer | Due date of the purchase (Unix epoch, e.g., `1619740800`). |
| refund_availability | string | Refund availability (e.g., `all`). |
| refundable_amount | integer | Amount available for refund (e.g., `123`). |
| currency_conversion | object | Currency conversion details. |
| currency_conversion.original_currency | string | Original currency of the purchase. |
| currency_conversion.original_amount | integer | Original amount before conversion (e.g., `123`). |
| currency_conversion.exchange_rate | integer | Exchange rate used (e.g., `123`). |
| payment_method_whitelist | array<string> | List of allowed payment methods (e.g., `["<string>"]`). |
| success_redirect | string | URL for successful payment redirect. |
| failure_redirect | string | URL for failed payment redirect. |
| cancel_redirect | string | URL for canceled payment redirect. |
| success_callback | string | URL for success callback. |
| creator_agent | string | Agent that created the purchase. |
| platform | string | Platform used (e.g., `web`). |
| product | string | Product type (e.g., `purchases`). |
| created_from_ip | string | IP address from which the purchase was created. |
| invoice_url | string | URL of the invoice. |
| checkout_url | string | URL for checkout. |
| direct_post_url | string | URL for direct post. |
| marked_as_paid | boolean | Whether the purchase is marked as paid (e.g., `true`). |
| order_id | string | Order ID for the purchase. |

#### Example Response

```json
{
  "type": "<string>",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "<string>",
    "bank_code": "<string>",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "<string>",
    "personal_code": "<string>",
    "street_address": "<string>",
    "country": "<string>",
    "city": "<string>",
    "zip_code": "<string>",
    "state": "<string>",
    "shipping_street_address": "<string>",
    "shipping_country": "<string>",
    "shipping_city": "<string>",
    "shipping_zip_code": "<string>",
    "shipping_state": "<string>",
    "cc": [
      "jsmith@example.com"
    ],
    "bcc": [
      "jsmith@example.com"
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "purchase": {
    "currency": "<string>",
    "products": [
      {
        "name": "<string>",
        "quantity": 1,
        "price": 1,
        "discount": 1,
        "tax_percent": 0,
        "category": "<string>"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "<string>",
    "debt": 123,
    "subtotal_override": 123,
    "total_tax_override": 123,
    "total_discount_override": 123,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "<string>"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "<string>",
    "net_amount": 123,
    "fee_amount": 123,
    "pending_amount": 123,
    "pending_unfreeze_on": 1619740800,
    "description": "<string>",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "<string>",
    "legal_street_address": "<string>",
    "legal_country": "<string>",
    "legal_city": "<string>",
    "legal_zip_code": "<string>",
    "bank_accounts": [
      {
        "bank_account": "<string>",
        "bank_code": "<string>"
      }
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "transaction_data": {
    "payment_method": "<string>",
    "extra": {},
    "country": "<string>",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "<string>",
        "extra": {},
        "country": "<string>",
        "client_ip": "<string>",
        "processing_time": 1619740800,
        "error": {
          "code": "<string>",
          "message": "<string>"
        }
      }
    ]
  },
  "status": "created",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "<string>",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": false,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "<string>",
  "reference": "<string>",
  "notes": "<string>",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "<string>",
    "original_amount": 123,
    "exchange_rate": 123
  },
  "payment_method_whitelist": [
    "<string>"
  ],
  "success_redirect": "<string>",
  "failure_redirect": "<string>",
  "cancel_redirect": "<string>",
  "success_callback": "<string>",
  "creator_agent": "<string>",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "<string>",
  "invoice_url": "<string>",
  "checkout_url": "<string>",
  "direct_post_url": "<string>",
  "marked_as_paid": true,
  "order_id": "<string>"
}
```

## Mark a Purchase as Paid

This endpoint allows you to mark a purchase as paid. Upon successful execution, the purchase’s `status` will be set to `paid`, and the `marked_as_paid` field will be set to `true` to distinguish this purchase.

### Endpoint

**POST** `/purchases/{id}/mark_as_paid/`

### Authorizations

| Header | Type | Description | Required |
|--------|------|-------------|----------|
| Authorization | string | Bearer authentication header of the form `Bearer <token>`, where `<token>` is your auth token. | Yes |

### Path Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| id | string<uuid> | Object ID (UUID) of the purchase to mark as paid. | Yes |

### Request Body

**Content-Type:** `application/json`

| Field | Type | Description | Required |
|-------|------|-------------|----------|
| paid_on | integer | Timestamp when the payment was made (Unix epoch, e.g., `1635162311`). | Yes |

#### Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/{id}/mark_as_paid/ \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "paid_on": 1635162311
  }'
```

### Response

**Status Code:** `200 OK`

**Content-Type:** `application/json`

The response contains a record of a single purchase operation, which could originate from an e-commerce integration or an invoice sent. The Purchase object includes a `status` attribute set to `paid` and a `marked_as_paid` field set to `true`.

#### Response Body

| Field | Type | Description |
|-------|------|-------------|
| type | string | Type of the purchase. |
| id | string<uuid> | Unique identifier of the purchase. |
| created_on | integer | Timestamp when the purchase was created (Unix epoch). |
| updated_on | integer | Timestamp when the purchase was last updated (Unix epoch). |
| client | object | Details about the client. |
| client.bank_account | string | Client's bank account number. |
| client.bank_code | string | Client's bank code. |
| client.email | string | Client's email address (e.g., `jsmith@example.com`). |
| client.phone | string | Client's phone number (e.g., `+44 45643564564`). |
| client.full_name | string | Client's full name. |
| client.personal_code | string | Client's personal code. |
| client.street_address | string | Client's street address. |
| client.country | string | Client's country. |
| client.city | string | Client's city. |
| client.zip_code | string | Client's zip code. |
| client.state | string | Client's state. |
| client.shipping_street_address | string | Client's shipping street address. |
| client.shipping_country | string | Client's shipping country. |
| client.shipping_city | string | Client's shipping city. |
| client.shipping_zip_code | string | Client's shipping zip code. |
| client.shipping_state | string | Client's shipping state. |
| client.cc | array<string> | List of email addresses to CC (e.g., `["jsmith@example.com"]`). |
| client.bcc | array<string> | List of email addresses to BCC (e.g., `["jsmith@example.com"]`). |
| client.legal_name | string | Client's legal name. |
| client.brand_name | string | Client's brand name. |
| client.registration_number | string | Client's registration number. |
| client.tax_number | string | Client's tax number. |
| purchase | object | Details about the purchase. |
| purchase.currency | string | Currency of the purchase. |
| purchase.products | array<object> | List of products in the purchase. |
| purchase.products[].name | string | Product name. |
| purchase.products[].quantity | integer | Product quantity (e.g., `1`). |
| purchase.products[].price | integer | Product price (e.g., `1`). |
| purchase.products[].discount | integer | Product discount (e.g., `1`). |
| purchase.products[].tax_percent | integer | Tax percentage for the product (e.g., `0`). |
| purchase.products[].category | string | Product category. |
| purchase.total | integer | Total amount of the purchase (e.g., `123`). |
| purchase.language | string | Language of the purchase. Default value is controlled in the Company -> Brand section of the merchant portal (default is `en`). |
| purchase.notes | string | Additional notes for the purchase. |
| purchase.debt | integer | Debt amount (e.g., `123`). |
| purchase.subtotal_override | integer | Override for the subtotal (e.g., `123`). |
| purchase.total_tax_override | integer | Override for the total tax (e.g., `123`). |
| purchase.total_discount_override | integer | Override for the total discount (e.g., `123`). |
| purchase.total_override | integer | Override for the total amount (e.g., `123`). |
| purchase.request_client_details | array | List of client details to request (empty by default). |
| purchase.timezone | string | Timezone of the purchase (e.g., `Europe/Oslo`). |
| purchase.due_strict | boolean | Whether the due date is strict (e.g., `false`). |
| purchase.email_message | string | Custom email message for the purchase. |
| payment | object | Payment details. |
| payment.is_outgoing | boolean | Whether the payment is outgoing (e.g., `false`). |
| payment.payment_type | string | Type of payment (e.g., `purchase`). |
| payment.amount | integer | Payment amount (e.g., `123`). |
| payment.currency | string | Payment currency. |
| payment.net_amount | integer | Net payment amount (e.g., `123`). |
| payment.fee_amount | integer | Fee amount (e.g., `123`). |
| payment.pending_amount | integer | Pending payment amount (e.g., `123`). |
| payment.pending_unfreeze_on | integer | Timestamp when pending amount unfreezes (Unix epoch, e.g., `1619740800`). |
| payment.description | string | Payment description. |
| payment.paid_on | integer | Timestamp when payment was made (Unix epoch, e.g., `1635162311`). |
| payment.remote_paid_on | integer | Timestamp when payment was processed remotely (Unix epoch, e.g., `1619740800`). |
| issuer_details | object | Details about the issuer. |
| issuer_details.website | string | Issuer's website. |
| issuer_details.legal_street_address | string | Issuer's legal street address. |
| issuer_details.legal_country | string | Issuer's legal country. |
| issuer_details.legal_city | string | Issuer's legal city. |
| issuer_details.legal_zip_code | string | Issuer's legal zip code. |
| issuer_details.bank_accounts | array<object> | List of issuer's bank accounts. |
| issuer_details.bank_accounts[].bank_account | string | Bank account number. |
| issuer_details.bank_accounts[].bank_code | string | Bank code. |
| issuer_details.legal_name | string | Issuer's legal name. |
| issuer_details.brand_name | string | Issuer's brand name. |
| issuer_details.registration_number | string | Issuer's registration number. |
| issuer_details.tax_number | string | Issuer's tax number. |
| transaction_data | object | Transaction details. |
| transaction_data.payment_method | string | Payment method used. |
| transaction_data.extra | object | Additional transaction data (empty object by default). |
| transaction_data.country | string | Country of the transaction. |
| transaction_data.attempts | array<object> | List of transaction attempts. |
| transaction_data.attempts[].type | string | Type of attempt (e.g., `execute`). |
| transaction_data.attempts[].successful | boolean | Whether the attempt was successful (e.g., `true`). |
| transaction_data.attempts[].payment_method | string | Payment method for the attempt. |
| transaction_data.attempts[].extra | object | Additional data for the attempt (empty object by default). |
| transaction_data.attempts[].country | string | Country of the attempt. |
| transaction_data.attempts[].client_ip | string | Client's IP address. |
| transaction_data.attempts[].processing_time | integer | Processing time of the attempt (Unix epoch, e.g., `1619740800`). |
| transaction_data.attempts[].error | object | Error details for the attempt. |
| transaction_data.attempts[].error.code | string | Error code. |
| transaction_data.attempts[].error.message | string | Error message. |
| status | string | Current status of the purchase (set to `paid` after this operation). |
| status_history | array<object> | History of status changes. |
| status_history[].status | string | Status at a specific point (e.g., `paid`). |
| status_history[].timestamp | integer | Timestamp of the status change (Unix epoch, e.g., `1619740800`). |
| status_history[].related_object | object | Related object for the status change. |
| status_history[].related_object.type | string | Type of the related object. |
| status_history[].related_object.id | string<uuid> | ID of the related object (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| viewed_on | integer | Timestamp when the purchase was viewed (Unix epoch, e.g., `1619740800`). |
| company_id | string<uuid> | ID of the company (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| is_test | boolean | Whether the purchase is in test mode (e.g., `true`). |
| user_id | string<uuid> | ID of the user (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| brand_id | string<uuid> | ID of the brand (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| billing_template_id | string<uuid> | ID of the billing template (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| client_id | string<uuid> | ID of the client (can be `null`). |
| send_receipt | boolean | Whether to send a receipt (e.g., `false`). |
| is_recurring_token | boolean | Whether the purchase is a recurring token (e.g., `true`). |
| recurring_token | string<uuid> | ID of the recurring token (e.g., `3c90c3cc-0d44-4b50-8888-8dd25736052a`). |
| skip_capture | boolean | Whether to skip capture (e.g., `false`). |
| force_recurring | boolean | Whether to force recurring payment (e.g., `false`). |
| reference_generated | string | Generated reference for the purchase. |
| reference | string | Reference for the purchase. |
| notes | string | Additional notes for the purchase. |
| issued | string | Date the purchase was issued (e.g., `2020-04-30`). |
| due | integer | Due date of the purchase (Unix epoch, e.g., `1619740800`). |
| refund_availability | string | Refund availability (e.g., `all`). |
| refundable_amount | integer | Amount available for refund (e.g., `123`). |
| currency_conversion | object | Currency conversion details. |
| currency_conversion.original_currency | string | Original currency of the purchase. |
| currency_conversion.original_amount | integer | Original amount before conversion (e.g., `123`). |
| currency_conversion.exchange_rate | integer | Exchange rate used (e.g., `123`). |
| payment_method_whitelist | array<string> | List of allowed payment methods (e.g., `["<string>"]`). |
| success_redirect | string | URL for successful payment redirect. |
| failure_redirect | string | URL for failed payment redirect. |
| cancel_redirect | string | URL for canceled payment redirect. |
| success_callback | string | URL for success callback. |
| creator_agent | string | Agent that created the purchase. |
| platform | string | Platform used (e.g., `web`). |
| product | string | Product type (e.g., `purchases`). |
| created_from_ip | string | IP address from which the purchase was created. |
| invoice_url | string | URL of the invoice. |
| checkout_url | string | URL for checkout. |
| direct_post_url | string | URL for direct post. |
| marked_as_paid | boolean | Whether the purchase is marked as paid (set to `true` after this operation). |
| order_id | string | Order ID for the purchase. |

#### Example Response

```json
{
  "type": "<string>",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "<string>",
    "bank_code": "<string>",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "<string>",
    "personal_code": "<string>",
    "street_address": "<string>",
    "country": "<string>",
    "city": "<string>",
    "zip_code": "<string>",
    "state": "<string>",
    "shipping_street_address": "<string>",
    "shipping_country": "<string>",
    "shipping_city": "<string>",
    "shipping_zip_code": "<string>",
    "shipping_state": "<string>",
    "cc": [
      "jsmith@example.com"
    ],
    "bcc": [
      "jsmith@example.com"
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "purchase": {
    "currency": "<string>",
    "products": [
      {
        "name": "<string>",
        "quantity": 1,
        "price": 1,
        "discount": 1,
        "tax_percent": 0,
        "category": "<string>"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "<string>",
    "debt": 123,
    "subtotal_override": 123,
    "total_tax_override": 123,
    "total_discount_override": 123,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "<string>"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "<string>",
    "net_amount": 123,
    "fee_amount": 123,
    "pending_amount": 123,
    "pending_unfreeze_on": 1619740800,
    "description": "<string>",
    "paid_on": 1635162311,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "<string>",
    "legal_street_address": "<string>",
    "legal_country": "<string>",
    "legal_city": "<string>",
    "legal_zip_code": "<string>",
    "bank_accounts": [
      {
        "bank_account": "<string>",
        "bank_code": "<string>"
      }
    ],
    "legal_name": "<string>",
    "brand_name": "<string>",
    "registration_number": "<string>",
    "tax_number": "<string>"
  },
  "transaction_data": {
    "payment_method": "<string>",
    "extra": {},
    "country": "<string>",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "<string>",
        "extra": {},
        "country": "<string>",
        "client_ip": "<string>",
        "processing_time": 1619740800,
        "error": {
          "code": "<string>",
          "message": "<string>"
        }
      }
    ]
  },
  "status": "paid",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "<string>",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "paid",
      "timestamp": 1635162311,
      "related_object": {
        "type": "<string>",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "<string>",
  "reference": "<string>",
  "notes": "<string>",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "<string>",
    "original_amount": 123,
    "exchange_rate": 123
  },
  "payment_method_whitelist": [
    "<string>"
  ],
  "success_redirect": "<string>",
  "failure_redirect": "<string>",
  "cancel_redirect": "<string>",
  "success_callback": "<string>",
  "creator_agent": "<string>",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "<string>",
  "invoice_url": "<string>",
  "checkout_url": "<string>",
  "direct_post_url": "<string>",
  "marked_as_paid": true,
  "order_id": "<string>"
}
```
# Create a Purchase

This endpoint allows you to create a new purchase in the CHIP system. The purchase object represents a payment request that can be processed through various payment methods.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Integrate the API from the server-side to avoid exposing your secret key to the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. Below are the parameters for creating a purchase.

### Parameters

| Parameter                     | Type    | Required | Description                                                                 |
|-------------------------------|---------|----------|-----------------------------------------------------------------------------|
| `brand_id`                    | String  | Yes      | UUID of the brand associated with the purchase.                             |
| `client`                      | Object  | Yes      | Details of the client making the purchase. See [Client Object](#client-object). |
| `products`                    | Array   | Yes      | List of products included in the purchase. See [Product Object](#product-object). |
| `currency`                    | String  | Yes      | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `total`                       | Integer | Yes      | Total amount in the smallest indivisible units of the currency (e.g., cents for EUR, no decimals for MYR). |
| `reference`                   | String  | No       | Merchant’s reference for the purchase.                                      |
| `success_redirect`            | String  | No       | URL to redirect the client after a successful payment.                      |
| `failure_redirect`            | String  | No       | URL to redirect the client after a failed payment.                          |
| `cancel_redirect`             | String  | No       | URL to redirect the client after canceling the payment.                     |
| `success_callback`            | String  | No       | URL to receive a POST callback with purchase data after successful payment. |
| `payment_method_whitelist`    | Array   | No       | List of allowed payment method names (from `/payment_methods/` endpoint).   |
| `skip_capture`                | Boolean | No       | If `true`, delays capture for manual processing (for supported methods).    |
| `recurring_token`             | String  | No       | Token for recurring payments (requires agreement with CHIP).                |
| `is_recurring_token`          | Boolean | No       | If `true`, creates a recurring token instead of charging immediately.       |
| `description`                 | String  | No       | Human-readable description of the purchase.                                 |
| `due`                         | Integer | No       | Unix timestamp for when the purchase is due.                                |
| `email_template_id`           | String  | No       | UUID of the email template to use for notifications.                        |
| `is_test`                     | Boolean | No       | If `true`, creates a test purchase (only with test API keys).               |

### Client Object

The `client` parameter is an object containing details about the client making the purchase.

| Attribute        | Type   | Required | Description                                                  |
|------------------|--------|----------|--------------------------------------------------------------|
| `email`          | String | Yes      | Client’s email address.                                      |
| `full_name`      | String | No       | Client’s full name.                                          |
| `phone_number`   | String | No       | Client’s phone number.                                       |
| `street_address` | String | No       | Client’s street address.                                     |
| `country`        | String | No       | Client’s country code in ISO 3166-1 alpha-2 format (e.g., `MY`). |
| `city`           | String | No       | Client’s city.                                               |
| `zip_code`       | String | No       | Client’s postal/ZIP code.                                    |

### Product Object

The `products` parameter is an array of objects, each representing a product in the purchase.

| Attribute   | Type    | Required | Description                                                  |
|-------------|---------|----------|--------------------------------------------------------------|
| `name`      | String  | Yes      | Name of the product.                                         |
| `quantity`  | Integer | Yes      | Number of units of the product.                              |
| `price`     | Integer | Yes      | Price per unit in the smallest indivisible units of the currency. |

## Example Request

```json
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "brand_id": "75a76529-91c7-4d98-90a9-8a641d70ee52",
    "client": {
      "email": "client@example.com",
      "full_name": "John Doe",
      "phone_number": "+60123456789",
      "street_address": "123 Main Street",
      "country": "MY",
      "city": "Kuala Lumpur",
      "zip_code": "50000"
    },
    "products": [
      {
        "name": "Widget",
        "quantity": 2,
        "price": 500
      }
    ],
    "currency": "MYR",
    "total": 1000,
    "reference": "ORDER123",
    "success_redirect": "https://example.com/success",
    "failure_redirect": "https://example.com/failure",
    "cancel_redirect": "https://example.com/cancel",
    "success_callback": "https://example.com/callback",
    "payment_method_whitelist": ["card", "fpx"],
    "skip_capture": false,
    "description": "Purchase of 2 widgets",
    "is_test": true
  }'
```

## Response

Upon successful creation, the API returns a JSON object with details of the created purchase.

### Response Attributes

| Attribute            | Type    | Description                                                  |
|----------------------|---------|--------------------------------------------------------------|
| `id`                 | String  | UUID of the created purchase.                                |
| `brand_id`           | String  | UUID of the associated brand.                                |
| `client`             | Object  | Client details as provided in the request.                   |
| `products`           | Array   | List of products as provided in the request.                 |
| `currency`           | String  | Currency code.                                               |
| `total`              | Integer | Total amount.                                                |
| `status`             | String  | Status of the purchase (e.g., `created`, `paid`).            |
| `checkout_url`       | String  | URL to redirect the client for payment processing.           |
| `reference`          | String  | Merchant’s reference, if provided.                           |
| `success_redirect`   | String  | Success redirect URL, if provided.                           |
| `failure_redirect`   | String  | Failure redirect URL, if provided.                           |
| `cancel_redirect`    | String  | Cancel redirect URL, if provided.                            |
| `success_callback`   | String  | Success callback URL, if provided.                           |
| `payment_method_whitelist` | Array | Whitelisted payment methods, if provided.                    |
| `skip_capture`       | Boolean | Skip capture setting, if provided.                           |
| `recurring_token`    | String  | Recurring token, if provided.                                |
| `is_recurring_token` | Boolean | Recurring token creation setting, if provided.               |
| `description`        | String  | Purchase description, if provided.                           |
| `due`                | Integer | Due timestamp, if provided.                                  |
| `email_template_id`  | String  | Email template UUID, if provided.                            |
| `is_test`            | Boolean | Test purchase indicator, if provided.                        |
| `created_on`         | Integer | Unix timestamp of purchase creation.                         |
| `updated_on`         | Integer | Unix timestamp of last update.                               |

### Example Response

```json
{
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "75a76529-91c7-4d98-90a9-8a641d70ee52",
  "client": {
    "email": "client@example.com",
    "full_name": "John Doe",
    "phone_number": "+60123456789",
    "street_address": "123 Main Street",
    "country": "MY",
    "city": "Kuala Lumpur",
    "zip_code": "50000"
  },
  "products": [
    {
      "name": "Widget",
      "quantity": 2,
      "price": 500
    }
  ],
  "currency": "MYR",
  "total": 1000,
  "status": "created",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "reference": "ORDER123",
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "payment_method_whitelist": ["card", "fpx"],
  "skip_capture": false,
  "description": "Purchase of 2 widgets",
  "is_test": true,
  "created_on": 1619740800,
  "updated_on": 1619740800
}
```

## Notes

- **Total Amount**: The `total` must match the sum of `price` × `quantity` for all products in the `products` array.
- **Payment Methods**: Use the `/payment_methods/` endpoint to retrieve available payment methods for the `payment_method_whitelist`.
- **Recurring Payments**: The `recurring_token` and `is_recurring_token` fields require prior agreement with CHIP.
- **Test Mode**: Use test API keys to create test purchases by setting `is_test: true`.
- **Redirects and Callbacks**: Ensure redirect and callback URLs are valid and secure (HTTPS).

# Retrieve a Purchase

This endpoint allows you to retrieve the details of a specific purchase in the CHIP system by its unique ID. The purchase object represents a payment request, such as a transaction from an e-commerce integration or an invoice sent, with a status like `created`, `paid`, or `refunded`.

## Endpoint

```
GET https://gate.chip-in.asia/api/v1/purchases/{id}/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase. |

## Example Request

```bash
curl --request GET \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object with detailed information about the purchase.

### Response Status

- **200 OK**: The purchase record is successfully retrieved, returned in `application/json` format.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., purchase).                                        |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `created`, `paid`, `refunded`).                |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture is skipped for manual processing.                      |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `created`).                        |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "card",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "card",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1619740800,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "created",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["card"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purchase Status**: The `status` field indicates the current state of the purchase (e.g., `created`, `paid`, `refunded`).
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Recurring Tokens**: The `is_recurring_token` and `recurring_token` fields are relevant for recurring payments, requiring prior agreement with CHIP.
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint.

# Cancel a Pending Purchase

This endpoint allows you to cancel a pending purchase in the CHIP system by its unique ID. Canceling a purchase ensures that it cannot be paid, effectively voiding the payment request. This is useful for transactions originating from e-commerce integrations or invoices that are no longer valid.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/cancel/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase to cancel. |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/cancel/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object with details of the canceled purchase. The `status` field will typically reflect the canceled state (e.g., `canceled`).

### Response Status

- **200 OK**: The purchase has been successfully canceled, returned in `application/json` format.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., `purchase`).                                      |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `canceled`, `created`, `paid`, `refunded`).   |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture is skipped for manual processing.                      |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `canceled`).                       |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "card",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "card",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1619740800,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "canceled",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "canceled",
      "timestamp": 1619740900,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["card"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purpose**: This endpoint ensures that a pending purchase cannot be paid, effectively voiding it. It is applicable only to purchases in a state where payment is still possible (e.g., `created`).
- **Status Update**: After cancellation, the `status` field in the response will reflect `canceled`, and the `status_history` will include a new entry for the cancellation event.
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Recurring Tokens**: The `is_recurring_token` and `recurring_token` fields are relevant for recurring payments, requiring prior agreement with CHIP.
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint.

# Release Funds on Hold

This endpoint allows you to release funds that are on hold for a specific purchase in the CHIP system, identified by its unique ID. Funds are typically placed on hold (authenticated) when a purchase is created with `skip_capture: true` and the client submits the payment form. This endpoint is used to release those reserved funds, effectively canceling the payment authorization.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/release/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase with funds to release. |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/release/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object with details of the purchase, reflecting the updated status of the funds. The `status` field will typically be `released` upon successful completion, or `pending_release` if the operation is still processing.

### Response Status

- **200 OK**: The release request was accepted, returned in `application/json` format. The purchase `status` may be `pending_release` if processing is delayed or `released` if completed.
- **400 Bad Request**: Indicates a `purchase_release_error` due to a payment processing issue. Check the `transaction_data.attempts` array in a subsequent `GET /purchases/{id}/` request for error details.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., `purchase`).                                      |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `pending_release`, `released`, `created`, `paid`, `refunded`). |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture was skipped for manual processing.                     |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `pending_release`, `released`).    |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "card",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "card",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1619740800,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "released",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "released",
      "timestamp": 1619740900,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["card"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purpose**: This endpoint releases funds that are on hold for a purchase with `status: hold`, typically set by creating a purchase with `skip_capture: true` and a submitted payment form.
- **Status Updates**: 
  - If the release is successful, the `status` field will be `released`, and a `purchase.released` webhook callback will be sent.
  - If processing is delayed (e.g., on the acquirer’s side), the `status` will be `pending_release`, and a `purchase.pending_release` webhook callback will be sent.
  - If the release fails, a 400 response with `purchase_release_error` is returned. Check the `transaction_data.attempts` array (newest first) in a `GET /purchases/{id}/` request for error details in the `error` object.
- **Webhooks**: Subscribe to `purchase.released` and `purchase.pending_release` webhook events to be notified of status changes.
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Recurring Tokens**: The `is_recurring_token` and `recurring_token` fields are relevant for recurring payments, requiring prior agreement with CHIP.
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint.

# Capture a Previously Authorized Payment

This endpoint allows you to capture funds that are on hold for a specific purchase in the CHIP system, identified by its unique ID. Funds are typically placed on hold (authorized) when a purchase is created with `skip_capture: true` and the client submits the payment form. This endpoint is used to capture those reserved funds, completing the payment process.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/capture/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase with funds to capture. |

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. By default, the full authorized amount is captured, but you can specify a partial amount.

| Parameter | Type    | Required | Description                                                  |
|-----------|---------|----------|--------------------------------------------------------------|
| `amount`  | Integer | No       | Amount to capture in the smallest currency unit (optional). If not provided, the full authorized amount is captured. |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/capture/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "amount": 123
  }'
```

## Response

Upon a successful request, the API returns a JSON object with details of the purchase, reflecting the updated status of the funds. The `status` field will typically be `paid` upon successful capture, or `pending_capture` if the operation is still processing.

### Response Status

- **200 OK**: The capture request was accepted, returned in `application/json` format. The purchase `status` may be `pending_capture` if processing is delayed or `paid` if completed.
- **400 Bad Request**: Indicates a `purchase_capture_error` due to a payment processing issue. Check the `transaction_data.attempts` array in a subsequent `GET /purchases/{id}/` request for error details in the `error` object.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., `purchase`).                                      |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `pending_capture`, `paid`, `created`, `refunded`). |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture was skipped for manual processing.                     |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `pending_capture`, `paid`).        |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "card",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "card",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1619740800,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "paid",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "paid",
      "timestamp": 1619740900,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["card"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purpose**: This endpoint captures funds that are on hold for a purchase with `status: hold`, typically set by creating a purchase with `skip_capture: true` and a submitted payment form.
- **Status Updates**: 
  - If the capture is successful, the `status` field will be `paid`, and a `purchase.captured` webhook callback will be sent.
  - If processing is delayed (e.g., on the acquirer’s side), the `status` will be `pending_capture`, and a `purchase.pending_capture` webhook callback will be sent.
  - If the capture fails, a 400 response with `purchase_capture_error` is returned. Check the `transaction_data.attempts` array (newest first) in a `GET /purchases/{id}/` request for error details in the `error` object.
- **Webhooks**: Subscribe to `purchase.captured` and `purchase.pending_capture` webhook events to be notified of status changes.
- **Amount**: The `amount` parameter is optional; if omitted, the full authorized amount is captured.
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Recurring Tokens**: The `is_recurring_token` and `recurring_token` fields are relevant for recurring payments, requiring prior agreement with CHIP.
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint.

# Charge a Purchase Using a Saved Token

This endpoint allows you to charge a purchase in the CHIP system using a saved recurring token, identified by the purchase’s unique ID. The recurring token enables charging the purchase using the same payment method (e.g., the same card) as a previous purchase that has `is_recurring_token: true`.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/charge/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase to charge. |

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. The `recurring_token` specifies the ID of a previous purchase with `is_recurring_token: true`.

| Parameter         | Type    | Required | Description                                                  |
|-------------------|---------|----------|--------------------------------------------------------------|
| `recurring_token` | String (UUID) | Yes      | The ID of a purchase with `is_recurring_token: true`, used to charge the payment with the same method. |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/charge/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "recurring_token": "ea582899-78ec-4c3a-9cb3-08f922e556b6"
  }'
```

## Response

Upon a successful request, the API returns a JSON object with details of the charged purchase. The `status` field will typically be `paid` upon successful charge, or `pending_charge` if the operation is still processing.

### Response Status

- **200 OK**: The charge request was accepted, returned in `application/json` format. The purchase `status` may be `pending_charge` if processing is delayed or `paid` if completed.
- **400 Bad Request**: Indicates a `purchase_charge_error` due to a payment processing issue. Check the `transaction_data.attempts` array in a subsequent `GET /purchases/{id}/` request for error details in the `error` object.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., `purchase`).                                      |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `pending_charge`, `paid`, `created`, `refunded`). |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture was skipped for manual processing.                     |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `pending_charge`, `paid`).         |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "card",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "card",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1619740800,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "paid",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "paid",
      "timestamp": 1619740900,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["card"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purpose**: This endpoint charges a purchase using a saved `recurring_token`, allowing payment with the same method (e.g., card) as a previous purchase with `is_recurring_token: true`. The token must be from a purchase with prior agreement for recurring payments with CHIP.
- **Status Updates**: 
  - If the charge is successful, the `status` field will be `paid`, and a `purchase.paid` webhook callback will be sent.
  - If processing is delayed (e.g., on the acquirer’s side), the `status` will be `pending_charge`, and a `purchase.pending_charge` webhook callback will be sent.
  - If the charge fails, a `purchase.payment_failure` webhook callback will be sent, and a 400 response with `purchase_charge_error` is returned. Check the `transaction_data.attempts` array (newest first) in a `GET /purchases/{id}/` request for error details in the `error` object.
- **Webhooks**: Subscribe to `purchase.paid`, `purchase.pending_charge`, and `purchase.payment_failure` webhook events to be notified of status changes.
- **Recurring Token**: The `recurring_token` in the request body must reference a valid purchase ID with `is_recurring_token: true`. This requires prior setup with CHIP for recurring payments.
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint.

# Refund a Paid Purchase

This endpoint allows you to refund a paid purchase in the CHIP system, identified by its unique ID. You can request a full or partial refund by specifying an optional `amount` in the request body. The refund generates a new Payment object, which is returned in the response.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/refund/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase to refund. |

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. The `amount` parameter is optional for partial refunds.

| Parameter | Type    | Required | Description                                                  |
|-----------|---------|----------|--------------------------------------------------------------|
| `amount`  | Integer | No       | Amount to refund in the smallest currency unit (optional). If not provided, the full refundable amount is refunded. |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/refund/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "amount": 120
  }'
```

## Response

Upon a successful request, the API returns a JSON object representing a Payment object generated by the refund. The associated purchase’s `status` may be updated to `pending_refund` if processing is delayed or `refunded` upon completion.

### Response Status

- **200 OK**: The refund request was accepted, returned in `application/json` format. The purchase `status` may be `pending_refund` if processing is delayed or `refunded` if completed.
- **400 Bad Request**: Indicates a `purchase_refund_error` due to a payment processing issue. Check the `transaction_data.attempts` array in a subsequent `GET /purchases/{id}/` request for error details in the `error` object.

### Response Attributes

The response contains a Payment object with the following attributes:

| Attribute            | Type    | Description                                                  |
|----------------------|---------|--------------------------------------------------------------|
| `type`               | String  | Type of the object (e.g., `payment`).                        |
| `id`                 | String  | UUID of the payment.                                         |
| `created_on`         | Integer | Unix timestamp when the payment was created.                 |
| `updated_on`         | Integer | Unix timestamp when the payment was last updated.            |
| `client`             | Object  | Client details. See [Client Object](#client-object).         |
| `payment`            | Object  | Payment details. See [Payment Object](#payment-object).      |
| `transaction_data`   | Object  | Transaction details. Empty object in this context.           |
| `related_to`         | Object  | Details of the related purchase. See [Related Object](#related-object). |
| `reference_generated`| String  | System-generated reference for the payment.                  |
| `reference`          | String  | Merchant-provided reference for the payment.                 |
| `account_id`         | String  | UUID of the account.                                         |
| `company_id`         | String  | UUID of the company.                                         |
| `is_test`            | Boolean | Indicates if the payment is a test transaction.              |
| `user_id`            | String  | UUID of the user who created the payment.                    |
| `brand_id`           | String  | UUID of the brand associated with the payment.               |

### Client Object

Details about the client associated with the payment.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Payment Object

Details about the payment for the refund.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing (true for refunds).     |
| `payment_type`         | String  | Type of payment (e.g., `refund`).                            |
| `amount`               | Integer | Refunded amount in the smallest currency unit.               |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Related Object

Details about the related purchase.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object (e.g., `purchase`).               |
| `id`      | String | UUID of the related purchase.                                |

### Example Response

```json
{
  "type": "payment",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "payment": {
    "is_outgoing": true,
    "payment_type": "refund",
    "amount": 120,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Refund for purchase",
    "paid_on": 1619740800,
    "remote_paid_on": 1619740800
  },
  "transaction_data": {},
  "related_to": {
    "type": "purchase",
    "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
  },
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "account_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
}
```

## Notes

- **Purpose**: This endpoint refunds a paid purchase, generating a new Payment object. The `amount` parameter is optional; if omitted, the full refundable amount (as indicated by the `refund_availability` and `refundable_amount` fields in the purchase) is refunded.
- **Status Updates**: 
  - If the refund is successful, the associated purchase’s `status` will be `refunded`, and a `payment.refunded` webhook callback will be sent with the generated Payment object.
  - If processing is delayed (e.g., on the acquirer’s side), the purchase `status` will be `pending_refund`, and a `purchase.pending_refund` webhook callback will be sent.
  - If the refund fails, a 400 response with `purchase_refund_error` is returned. Check the `transaction_data.attempts` array (newest first) in a `GET /purchases/{id}/` request for error details in the `error` object.
- **Webhooks**: Subscribe to `payment.refunded` and `purchase.pending_refund` webhook events to be notified of status changes.
- **Refund Availability**: Check the `refund_availability` field on the purchase to determine if a refund is possible. The `refundable_amount` field indicates the maximum amount available for refund.
- **Test Mode**: The `is_test` field indicates if the payment is a test transaction, created using a test API key.

# Mark a Purchase as Paid

This endpoint allows you to manually mark a purchase in the CHIP system as paid, identified by its unique ID. This is useful for scenarios where payment is received outside the CHIP system (e.g., via bank transfer or cash) and needs to be recorded. The endpoint sets the purchase’s `status` to `paid` and the `marked_as_paid` field to `true`.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/purchases/{id}/mark_as_paid/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the purchase to mark as paid. |

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. The `paid_on` parameter is required to specify when the payment was made.

| Parameter  | Type    | Required | Description                                                  |
|------------|---------|----------|--------------------------------------------------------------|
| `paid_on`  | Integer | Yes      | Unix timestamp indicating when the payment was made.          |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/purchases/3c90c3cc-0d44-4b50-8888-8dd25736052a/mark_as_paid/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "paid_on": 1635162311
  }'
```

## Response

Upon a successful request, the API returns a JSON object representing the updated purchase with its `status` set to `paid` and `marked_as_paid` set to `true`.

### Response Status

- **200 OK**: The request was successful, returned in `application/json` format. The purchase `status` is updated to `paid`, and `marked_as_paid` is set to `true`.

### Response Attributes

The response contains a comprehensive purchase object with the following attributes:

| Attribute                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| `type`                        | String  | Type of the object (e.g., `purchase`).                                      |
| `id`                          | String  | UUID of the purchase.                                                       |
| `created_on`                  | Integer | Unix timestamp when the purchase was created.                               |
| `updated_on`                  | Integer | Unix timestamp when the purchase was last updated.                          |
| `client`                      | Object  | Client details. See [Client Object](#client-object).                        |
| `purchase`                    | Object  | Purchase details. See [Purchase Object](#purchase-object).                  |
| `payment`                     | Object  | Payment details. See [Payment Object](#payment-object).                     |
| `issuer_details`              | Object  | Issuer details. See [Issuer Details Object](#issuer-details-object).        |
| `transaction_data`            | Object  | Transaction details. See [Transaction Data Object](#transaction-data-object). |
| `status`                      | String  | Status of the purchase (e.g., `paid`, `created`, `refunded`).               |
| `status_history`              | Array   | History of status changes. See [Status History Object](#status-history-object). |
| `viewed_on`                   | Integer | Unix timestamp when the purchase was viewed.                                |
| `company_id`                  | String  | UUID of the company.                                                        |
| `is_test`                     | Boolean | Indicates if the purchase is a test transaction.                            |
| `user_id`                     | String  | UUID of the user who created the purchase.                                  |
| `brand_id`                    | String  | UUID of the brand associated with the purchase.                             |
| `billing_template_id`         | String  | UUID of the billing template used.                                          |
| `client_id`                   | String  | UUID of the client, if applicable (null if not set).                        |
| `send_receipt`                | Boolean | Indicates if a receipt should be sent.                                      |
| `is_recurring_token`          | Boolean | Indicates if the purchase creates a recurring token.                        |
| `recurring_token`             | String  | UUID of the recurring token, if applicable.                                 |
| `skip_capture`                | Boolean | Indicates if capture was skipped for manual processing.                     |
| `force_recurring`             | Boolean | Indicates if recurring payment is forced.                                   |
| `reference_generated`         | String  | System-generated reference for the purchase.                                |
| `reference`                   | String  | Merchant-provided reference for the purchase.                               |
| `notes`                       | String  | Additional notes for the purchase.                                          |
| `issued`                      | String  | Date the purchase was issued (format: `YYYY-MM-DD`).                        |
| `due`                         | Integer | Unix timestamp when the purchase is due.                                    |
| `refund_availability`         | String  | Refund availability status (e.g., `all`).                                   |
| `refundable_amount`           | Integer | Amount available for refund in the smallest currency unit.                  |
| `currency_conversion`         | Object  | Currency conversion details. See [Currency Conversion Object](#currency-conversion-object). |
| `payment_method_whitelist`    | Array   | List of allowed payment method names.                                       |
| `success_redirect`            | String  | URL for redirect after successful payment.                                  |
| `failure_redirect`            | String  | URL for redirect after failed payment.                                      |
| `cancel_redirect`             | String  | URL for redirect after canceled payment.                                    |
| `success_callback`            | String  | URL for POST callback after successful payment.                             |
| `creator_agent`               | String  | Agent that created the purchase (e.g., browser user agent).                 |
| `platform`                    | String  | Platform used (e.g., `web`).                                                |
| `product`                     | String  | Product type (e.g., `purchases`).                                           |
| `created_from_ip`             | String  | IP address from which the purchase was created.                             |
| `invoice_url`                 | String  | URL for the invoice, if available.                                          |
| `checkout_url`                | String  | URL for payment processing.                                                 |
| `direct_post_url`             | String  | URL for direct POST payment submission.                                     |
| `marked_as_paid`              | Boolean | Indicates if the purchase was manually marked as paid.                      |
| `order_id`                    | String  | Merchant’s order ID for the purchase.                                       |

### Client Object

Details about the client associated with the purchase.

| Attribute                   | Type   | Description                                                  |
|-----------------------------|--------|--------------------------------------------------------------|
| `bank_account`              | String | Client’s bank account number.                                |
| `bank_code`                 | String | Client’s bank code.                                          |
| `email`                     | String | Client’s email address.                                      |
| `phone`                     | String | Client’s phone number (e.g., `+44 45643564564`).             |
| `full_name`                 | String | Client’s full name.                                          |
| `personal_code`             | String | Client’s personal identification code.                       |
| `street_address`            | String | Client’s street address.                                     |
| `country`                   | String | Client’s country code (ISO 3166-1 alpha-2).                  |
| `city`                      | String | Client’s city.                                               |
| `zip_code`                  | String | Client’s postal/ZIP code.                                    |
| `state`                     | String | Client’s state or region.                                    |
| `shipping_street_address`   | String | Client’s shipping street address.                            |
| `shipping_country`          | String | Client’s shipping country code (ISO 3166-1 alpha-2).         |
| `shipping_city`             | String | Client’s shipping city.                                      |
| `shipping_zip_code`         | String | Client’s shipping postal/ZIP code.                           |
| `shipping_state`            | String | Client’s shipping state or region.                           |
| `cc`                        | Array  | List of email addresses for carbon copy.                     |
| `bcc`                       | Array  | List of email addresses for blind carbon copy.               |
| `legal_name`                | String | Client’s legal name (for businesses).                        |
| `brand_name`                | String | Client’s brand name (for businesses).                        |
| `registration_number`       | String | Client’s business registration number.                       |
| `tax_number`                | String | Client’s tax identification number.                          |

### Purchase Object

Details about the purchase itself.

| Attribute                   | Type    | Description                                                                 |
|-----------------------------|---------|-----------------------------------------------------------------------------|
| `currency`                  | String  | ISO 4217 currency code (e.g., `MYR`, `EUR`).                                |
| `products`                  | Array   | List of products. See [Product Object](#product-object).                    |
| `total`                     | Integer | Total amount in the smallest currency unit.                                 |
| `language`                  | String  | Language for the purchase (default set in merchant portal, e.g., `en`).     |
| `notes`                     | String  | Additional notes for the purchase.                                          |
| `debt`                      | Integer | Outstanding debt amount in the smallest currency unit.                      |
| `subtotal_override`         | Integer | Overridden subtotal amount, if applicable.                                  |
| `total_tax_override`        | Integer | Overridden total tax amount, if applicable.                                 |
| `total_discount_override`   | Integer | Overridden total discount amount, if applicable.                            |
| `total_override`            | Integer | Overridden total amount, if applicable.                                     |
| `request_client_details`    | Array   | List of client details requested (empty if none).                           |
| `timezone`                  | String  | Timezone for the purchase (e.g., `Europe/Oslo`).                            |
| `due_strict`                | Boolean | Indicates if the due date is strictly enforced.                             |
| `email_message`             | String  | Custom email message for the purchase.                                      |

#### Product Object

Details about each product in the purchase.

| Attribute      | Type    | Description                                                  |
|----------------|---------|--------------------------------------------------------------|
| `name`         | String  | Name of the product.                                         |
| `quantity`     | Integer | Number of units.                                             |
| `price`        | Integer | Price per unit in the smallest currency unit.                |
| `discount`     | Integer | Discount applied per unit in the smallest currency unit.     |
| `tax_percent`  | Integer | Tax percentage applied to the product.                       |
| `category`     | String  | Product category.                                            |

### Payment Object

Details about the payment for the purchase.

| Attribute              | Type    | Description                                                  |
|------------------------|---------|--------------------------------------------------------------|
| `is_outgoing`          | Boolean | Indicates if the payment is outgoing.                        |
| `payment_type`         | String  | Type of payment (e.g., `purchase`).                          |
| `amount`               | Integer | Payment amount in the smallest currency unit.                |
| `currency`             | String  | Currency code.                                               |
| `net_amount`           | Integer | Net amount after fees.                                       |
| `fee_amount`           | Integer | Fee amount charged.                                          |
| `pending_amount`       | Integer | Amount pending processing.                                   |
| `pending_unfreeze_on`  | Integer | Unix timestamp when pending amount is unfrozen.              |
| `description`          | String  | Payment description.                                         |
| `paid_on`              | Integer | Unix timestamp when payment was made.                        |
| `remote_paid_on`       | Integer | Unix timestamp when payment was confirmed remotely.          |

### Issuer Details Object

Details about the issuer (merchant) of the purchase.

| Attribute              | Type   | Description                                                  |
|------------------------|--------|--------------------------------------------------------------|
| `website`              | String | Issuer’s website URL.                                        |
| `legal_street_address` | String | Issuer’s legal street address.                               |
| `legal_country`        | String | Issuer’s legal country code (ISO 3166-1 alpha-2).            |
| `legal_city`           | String | Issuer’s legal city.                                         |
| `legal_zip_code`       | String | Issuer’s legal postal/ZIP code.                              |
| `bank_accounts`        | Array  | List of issuer’s bank accounts. See [Bank Account Object](#bank-account-object). |
| `legal_name`           | String | Issuer’s legal name.                                         |
| `brand_name`           | String | Issuer’s brand name.                                         |
| `registration_number`  | String | Issuer’s business registration number.                       |
| `tax_number`           | String | Issuer’s tax identification number.                          |

#### Bank Account Object

Details about each bank account of the issuer.

| Attribute      | Type   | Description                                                  |
|----------------|--------|--------------------------------------------------------------|
| `bank_account` | String | Bank account number.                                         |
| `bank_code`    | String | Bank code.                                                   |

### Transaction Data Object

Details about the transaction processing.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `payment_method` | String | Payment method used.                                         |
| `extra`          | Object | Additional transaction data (key-value pairs).               |
| `country`        | String | Country code (ISO 3166-1 alpha-2).                           |
| `attempts`       | Array  | List of transaction attempts. See [Attempt Object](#attempt-object). |

#### Attempt Object

Details about each transaction attempt.

| Attribute         | Type   | Description                                                  |
|-------------------|--------|--------------------------------------------------------------|
| `type`            | String | Type of attempt (e.g., `execute`).                           |
| `successful`      | Boolean | Indicates if the attempt was successful.                     |
| `payment_method`  | String | Payment method used in the attempt.                          |
| `extra`           | Object | Additional attempt data (key-value pairs).                   |
| `country`         | String | Country code (ISO 3166-1 alpha-2).                           |
| `client_ip`       | String | Client’s IP address during the attempt.                      |
| `processing_time` | Integer | Unix timestamp of the attempt.                               |
| `error`           | Object | Error details, if applicable. See [Error Object](#error-object). |

#### Error Object

Details about errors during transaction attempts.

| Attribute  | Type   | Description                                                  |
|------------|--------|--------------------------------------------------------------|
| `code`     | String | Error code.                                                  |
| `message`  | String | Error message.                                               |

### Status History Object

Details about each status change of the purchase.

| Attribute        | Type   | Description                                                  |
|------------------|--------|--------------------------------------------------------------|
| `status`         | String | Status at the time (e.g., `paid`, `created`).                |
| `timestamp`      | Integer | Unix timestamp of the status change.                         |
| `related_object` | Object | Related object details. See [Related Object](#related-object). |

#### Related Object

Details about the object related to the status change.

| Attribute | Type   | Description                                                  |
|-----------|--------|--------------------------------------------------------------|
| `type`    | String | Type of the related object.                                  |
| `id`      | String | UUID of the related object.                                  |

### Currency Conversion Object

Details about currency conversion, if applicable.

| Attribute           | Type    | Description                                                  |
|---------------------|---------|--------------------------------------------------------------|
| `original_currency` | String  | Original currency code.                                      |
| `original_amount`   | Integer | Amount in the original currency.                             |
| `exchange_rate`     | Integer | Exchange rate applied.                                       |

### Example Response

```json
{
  "type": "purchase",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1635162311,
  "client": {
    "bank_account": "1234567890",
    "bank_code": "BANK123",
    "email": "jsmith@example.com",
    "phone": "+44 45643564564",
    "full_name": "John Smith",
    "personal_code": "ABC123",
    "street_address": "123 Main Street",
    "country": "GB",
    "city": "London",
    "zip_code": "SW1A 1AA",
    "state": "England",
    "shipping_street_address": "456 Delivery Road",
    "shipping_country": "GB",
    "shipping_city": "London",
    "shipping_zip_code": "SW1A 1AA",
    "shipping_state": "England",
    "cc": ["jsmith@example.com"],
    "bcc": ["jsmith@example.com"],
    "legal_name": "John Smith Ltd",
    "brand_name": "Smith Co",
    "registration_number": "12345678",
    "tax_number": "GB123456789"
  },
  "purchase": {
    "currency": "GBP",
    "products": [
      {
        "name": "Widget",
        "quantity": 1,
        "price": 100,
        "discount": 10,
        "tax_percent": 20,
        "category": "Electronics"
      }
    ],
    "total": 123,
    "language": "en",
    "notes": "Urgent delivery required",
    "debt": 0,
    "subtotal_override": 100,
    "total_tax_override": 20,
    "total_discount_override": 10,
    "total_override": 123,
    "request_client_details": [],
    "timezone": "Europe/Oslo",
    "due_strict": false,
    "email_message": "Thank you for your purchase!"
  },
  "payment": {
    "is_outgoing": false,
    "payment_type": "purchase",
    "amount": 123,
    "currency": "GBP",
    "net_amount": 120,
    "fee_amount": 3,
    "pending_amount": 0,
    "pending_unfreeze_on": 1619740800,
    "description": "Payment for Widget",
    "paid_on": 1635162311,
    "remote_paid_on": 1635162311
  },
  "issuer_details": {
    "website": "https://example.com",
    "legal_street_address": "789 Business Avenue",
    "legal_country": "GB",
    "legal_city": "London",
    "legal_zip_code": "EC1A 1BB",
    "bank_accounts": [
      {
        "bank_account": "0987654321",
        "bank_code": "BANK456"
      }
    ],
    "legal_name": "Example Ltd",
    "brand_name": "Example Brand",
    "registration_number": "87654321",
    "tax_number": "GB987654321"
  },
  "transaction_data": {
    "payment_method": "manual",
    "extra": {},
    "country": "GB",
    "attempts": [
      {
        "type": "execute",
        "successful": true,
        "payment_method": "manual",
        "extra": {},
        "country": "GB",
        "client_ip": "192.168.1.1",
        "processing_time": 1635162311,
        "error": {
          "code": "",
          "message": ""
        }
      }
    ]
  },
  "status": "paid",
  "status_history": [
    {
      "status": "created",
      "timestamp": 1619740800,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    },
    {
      "status": "paid",
      "timestamp": 1635162311,
      "related_object": {
        "type": "purchase",
        "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a"
      }
    }
  ],
  "viewed_on": 1619740800,
  "company_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "is_test": true,
  "user_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "brand_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "billing_template_id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "client_id": null,
  "send_receipt": false,
  "is_recurring_token": true,
  "recurring_token": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "skip_capture": false,
  "force_recurring": false,
  "reference_generated": "REF123",
  "reference": "ORDER123",
  "notes": "Internal note",
  "issued": "2020-04-30",
  "due": 1619740800,
  "refund_availability": "all",
  "refundable_amount": 123,
  "currency_conversion": {
    "original_currency": "USD",
    "original_amount": 150,
    "exchange_rate": 0.82
  },
  "payment_method_whitelist": ["manual"],
  "success_redirect": "https://example.com/success",
  "failure_redirect": "https://example.com/failure",
  "cancel_redirect": "https://example.com/cancel",
  "success_callback": "https://example.com/callback",
  "creator_agent": "Mozilla/5.0",
  "platform": "web",
  "product": "purchases",
  "created_from_ip": "192.168.1.1",
  "invoice_url": "https://example.com/invoice",
  "checkout_url": "https://gate.chip-in.asia/checkout/3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "direct_post_url": "https://gate.chip-in.asia/direct_post",
  "marked_as_paid": true,
  "order_id": "ORD123"
}
```

## Notes

- **Purpose**: This endpoint manually sets a purchase’s `status` to `paid` and `marked_as_paid` to `true`, typically used when payment is received outside the CHIP system (e.g., via bank transfer or cash).
- **Status Updates**: The purchase’s `status` is updated to `paid`, and the `marked_as_paid` field is set to `true` to distinguish it from purchases paid through the CHIP system.
- **Payment Timestamp**: The `paid_on` field in the request body is required to record when the payment was made, updating the `payment.paid_on` and `payment.remote_paid_on` fields in the response.
- **Language Default**: The `language` field defaults to the setting in the merchant portal’s Company -> Brand section (default is `en` unless changed).
- **Test Mode**: The `is_test` field indicates if the purchase is a test transaction, created using a test API key.
- **Currency Conversion**: If applicable, the `currency_conversion` object provides details about the original currency and exchange rate.
- **Payment Methods**: The `payment_method_whitelist` lists allowed payment methods, which can be retrieved from the `/payment_methods/` endpoint. For this endpoint, the `payment_method` is typically set to `manual`.


# Create a Webhook

This endpoint allows you to create a webhook rule in the CHIP system to receive notifications about specific events (e.g., purchase-related events) at a designated callback URL. The webhook will send a POST request to the specified URL with the related object’s data when triggered.

## Endpoint

```
POST https://gate.chip-in.asia/api/v1/webhooks/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. It defines the webhook rule, including the events to listen for and the callback URL.

| Parameter     | Type    | Required | Description                                                  |
|---------------|---------|----------|--------------------------------------------------------------|
| `title`       | String  | Yes      | A descriptive name for the webhook.                          |
| `all_events`  | Boolean | Yes      | If `true`, the webhook listens for all available events; if `false`, only the events listed in `events` are monitored. |
| `public_key`  | String  | No       | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | Yes      | List of event types to monitor (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | Yes      | URL where the webhook sends POST requests with event data.   |

## Example Request

```bash
curl --request POST \
  --url https://gate.chip-in.asia/api/v1/webhooks/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "title": "Purchase Webhook",
    "all_events": false,
    "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
    "events": ["purchase.created"],
    "callback": "https://example.com/webhook"
}'
```

## Response

Upon a successful request, the API returns a JSON object representing the created webhook rule with a status code of 201.

### Response Status

- **201 Created**: The webhook was successfully created, returned in `application/json` format.

### Response Attributes

The response contains a webhook object with the following attributes:

| Attribute     | Type    | Description                                                  |
|---------------|---------|--------------------------------------------------------------|
| `type`        | String  | Type of the object (e.g., `webhook`).                        |
| `id`          | String  | UUID of the webhook.                                         |
| `created_on`  | Integer | Unix timestamp when the webhook was created.                 |
| `updated_on`  | Integer | Unix timestamp when the webhook was last updated.            |
| `title`       | String  | Descriptive name of the webhook.                             |
| `all_events`  | Boolean | Indicates if the webhook listens for all events (`true`) or specific events (`false`). |
| `public_key`  | String  | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | List of event types the webhook monitors (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | URL where the webhook sends POST requests with event data.   |

### Example Response

```json
{
  "type": "webhook",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "title": "Purchase Webhook",
  "all_events": false,
  "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
  "events": ["purchase.created"],
  "callback": "https://example.com/webhook"
}
```

## Notes

- **Purpose**: This endpoint creates a webhook rule to receive POST requests at the specified `callback` URL when the configured events are triggered. The payload includes the related object (e.g., a Purchase object for `purchase.*` events) and an `event_type` field indicating the specific event (e.g., `purchase.created`).
- **Event Types**: The `events` array specifies which events to monitor (e.g., `purchase.created`, `purchase.paid`, `purchase.refunded`). If `all_events` is `true`, the webhook listens for all available events, and the `events` array is ignored.
- **Test vs. Live**: Test webhooks (created with a test API key) only handle events from test purchases, and live webhooks only handle events from live purchases. They are separate and do not interact.
- **Webhook Payload**: When an event triggers the webhook, the callback URL receives a POST request with the related object’s data (e.g., a Purchase object) and an `event_type` field to identify the event.
- **Public Key**: The `public_key` can be used to verify the authenticity of webhook payloads by checking the signature. It should be provided in PEM format if used.
- **Security**: Ensure the `callback` URL is secure (HTTPS) and can handle POST requests. The webhook system does not support CORS, so client-side handling is not recommended.

# Retrieve a Webhook

This endpoint allows you to retrieve the details of a specific webhook rule in the CHIP system, identified by its unique ID. The webhook defines a rule for sending notifications about specific events (e.g., purchase-related events) to a designated callback URL.

## Endpoint

```
GET https://gate.chip-in.asia/api/v1/webhooks/{id}/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the webhook to retrieve. |

## Request Body

This endpoint does not require a request body.

## Example Request

```bash
curl --request GET \
  --url https://gate.chip-in.asia/api/v1/webhooks/3c90c3cc-0d44-4b50-8888-8dd25736052a/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object representing the webhook rule with a status code of 200.

### Response Status

- **200 OK**: The webhook was successfully retrieved, returned in `application/json` format.

### Response Attributes

The response contains a webhook object with the following attributes:

| Attribute     | Type    | Description                                                  |
|---------------|---------|--------------------------------------------------------------|
| `type`        | String  | Type of the object (e.g., `webhook`).                        |
| `id`          | String  | UUID of the webhook.                                         |
| `created_on`  | Integer | Unix timestamp when the webhook was created.                 |
| `updated_on`  | Integer | Unix timestamp when the webhook was last updated.            |
| `title`       | String  | Descriptive name of the webhook.                             |
| `all_events`  | Boolean | Indicates if the webhook listens for all events (`true`) or specific events (`false`). |
| `public_key`  | String  | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | List of event types the webhook monitors (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | URL where the webhook sends POST requests with event data.   |

### Example Response

```json
{
  "type": "webhook",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "title": "Purchase Webhook",
  "all_events": false,
  "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
  "events": ["purchase.created"],
  "callback": "https://example.com/webhook"
}
```

## Notes

- **Purpose**: This endpoint retrieves the details of a specific webhook rule, including its configuration for event monitoring and callback URL.
- **Event Types**: The `events` array lists the specific events the webhook monitors (e.g., `purchase.created`). If `all_events` is `true`, the webhook listens for all available events, and the `events` array is ignored.
- **Test vs. Live**: Test webhooks (created with a test API key) only handle events from test purchases, and live webhooks only handle events from live purchases. They are separate and do not interact.
- **Webhook Payload**: When an event triggers the webhook, the callback URL receives a POST request with the related object’s data (e.g., a Purchase object for `purchase.*` events) and an `event_type` field to identify the event.
- **Public Key**: The `public_key` can be used to verify the authenticity of webhook payloads by checking the signature. It is provided in PEM format if set during webhook creation.
- **Security**: Ensure the `callback` URL is secure (HTTPS) and can handle POST requests. The webhook system does not support CORS, so client-side handling is not recommended.

# Update a Webhook

This endpoint allows you to update an existing webhook rule in the CHIP system, identified by its unique ID. The webhook defines a rule for sending notifications about specific events (e.g., purchase-related events) to a designated callback URL.

## Endpoint

```
PUT https://gate.chip-in.asia/api/v1/webhooks/{id}/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the webhook to update. |

## Request Body

The request body must be sent in JSON format with the `Content-Type: application/json` header. It defines the updated webhook rule, including the events to listen for and the callback URL.

| Parameter     | Type    | Required | Description                                                  |
|---------------|---------|----------|--------------------------------------------------------------|
| `title`       | String  | Yes      | A descriptive name for the webhook.                          |
| `all_events`  | Boolean | Yes      | If `true`, the webhook listens for all available events; if `false`, only the events listed in `events` are monitored. |
| `public_key`  | String  | No       | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | Yes      | List of event types to monitor (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | Yes      | URL where the webhook sends POST requests with event data.   |

## Example Request

```bash
curl --request PUT \
  --url https://gate.chip-in.asia/api/v1/webhooks/3c90c3cc-0d44-4b50-8888-8dd25736052a/ \
  --header 'Authorization: Bearer <secret_key>' \
  --header 'Content-Type: application/json' \
  --data '{
    "title": "Updated Purchase Webhook",
    "all_events": false,
    "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
    "events": ["purchase.created"],
    "callback": "https://example.com/webhook"
}'
```

## Response

Upon a successful request, the API returns a JSON object representing the updated webhook rule with a status code of 200.

### Response Status

- **200 OK**: The webhook was successfully updated, returned in `application/json` format.

### Response Attributes

The response contains a webhook object with the following attributes:

| Attribute     | Type    | Description                                                  |
|---------------|---------|--------------------------------------------------------------|
| `type`        | String  | Type of the object (e.g., `webhook`).                        |
| `id`          | String  | UUID of the webhook.                                         |
| `created_on`  | Integer | Unix timestamp when the webhook was created.                 |
| `updated_on`  | Integer | Unix timestamp when the webhook was last updated.            |
| `title`       | String  | Descriptive name of the webhook.                             |
| `all_events`  | Boolean | Indicates if the webhook listens for all events (`true`) or specific events (`false`). |
| `public_key`  | String  | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | List of event types the webhook monitors (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | URL where the webhook sends POST requests with event data.   |

### Example Response

```json
{
  "type": "webhook",
  "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
  "created_on": 1619740800,
  "updated_on": 1619740800,
  "title": "Updated Purchase Webhook",
  "all_events": false,
  "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
  "events": ["purchase.created"],
  "callback": "https://example.com/webhook"
}
```

## Notes

- **Purpose**: This endpoint updates an existing webhook rule, allowing changes to the title, events monitored, public key, or callback URL. The webhook sends POST requests to the `callback` URL with the related object’s data (e.g., a Purchase object for `purchase.*` events) when triggered.
- **Event Types**: The `events` array specifies which events to monitor (e.g., `purchase.created`, `purchase.paid`, `purchase.refunded`). If `all_events` is `true`, the webhook listens for all available events, and the `events` array is ignored.
- **Test vs. Live**: Test webhooks (created or updated with a test API key) only handle events from test purchases, and live webhooks only handle events from live purchases. They are separate and do not interact.
- **Webhook Payload**: When an event triggers the webhook, the callback URL receives a POST request with the related object’s data and an `event_type` field to identify the event.
- **Public Key**: The `public_key` can be updated to a new PEM-formatted key for verifying webhook signatures. If not provided, the existing key (if any) remains unchanged.
- **Security**: Ensure the `callback` URL is secure (HTTPS) and can handle POST requests. The webhook system does not support CORS, so client-side handling is not recommended.

# Delete a Webhook

This endpoint allows you to delete a specific webhook rule in the CHIP system, identified by its unique ID. Deleting a webhook stops it from receiving notifications for any configured events.

## Endpoint

```
DELETE https://gate.chip-in.asia/api/v1/webhooks/{id}/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Path Parameters

| Parameter | Type        | Required | Description                     |
|-----------|-------------|----------|---------------------------------|
| `id`      | String (UUID) | Yes      | The unique ID of the webhook to delete. |

## Request Body

This endpoint does not require a request body.

## Example Request

```bash
curl --request DELETE \
  --url https://gate.chip-in.asia/api/v1/webhooks/3c90c3cc-0d44-4b50-8888-8dd25736052a/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a status code of 204 with no content, indicating the webhook was successfully deleted.

### Response Status

- **204 No Content**: The webhook was successfully deleted.

## Notes

- **Purpose**: This endpoint removes a webhook rule, stopping it from receiving POST requests for any configured events (e.g., `purchase.created`, `purchase.paid`).
- **Test vs. Live**: Deleting a test webhook (created with a test API key) only affects test event handling, and deleting a live webhook only affects live event handling. Test and live webhooks are separate and do not interact.
- **Impact**: After deletion, the webhook will no longer receive notifications, and any associated callback URL will stop receiving POST requests from the CHIP system.
- **Security**: Ensure the correct webhook ID is provided to avoid accidentally deleting the wrong webhook.

# List all Webhooks

This endpoint allows you to retrieve a list of all webhook rules configured in the CHIP system. Each webhook defines a rule for sending notifications about specific events (e.g., purchase-related events) to a designated callback URL.

## Endpoint

```
GET https://gate.chip-in.asia/api/v1/webhooks/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Request Body

This endpoint does not require a request body.

## Example Request

```bash
curl --request GET \
  --url https://gate.chip-in.asia/api/v1/webhooks/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object containing a list of webhook rules with a status code of 200. The response includes pagination links if applicable.

### Response Status

- **200 OK**: The list of webhooks was successfully retrieved, returned in `application/json` format.

### Response Attributes

The response is an object with the following attributes:

| Attribute   | Type   | Description                                                  |
|-------------|--------|--------------------------------------------------------------|
| `results`   | Array  | List of webhook objects. See [Webhook Object](#webhook-object). |
| `next`      | String | URL for the next page of results, if available.              |
| `previous`  | String | URL for the previous page of results, if available.          |

#### Webhook Object

Details about each webhook rule.

| Attribute     | Type    | Description                                                  |
|---------------|---------|--------------------------------------------------------------|
| `type`        | String  | Type of the object (e.g., `webhook`).                        |
| `id`          | String  | UUID of the webhook.                                         |
| `created_on`  | Integer | Unix timestamp when the webhook was created.                 |
| `updated_on`  | Integer | Unix timestamp when the webhook was last updated.            |
| `title`       | String  | Descriptive name of the webhook.                             |
| `all_events`  | Boolean | Indicates if the webhook listens for all events (`true`) or specific events (`false`). |
| `public_key`  | String  | Public key for verifying webhook signatures (PEM format).     |
| `events`      | Array   | List of event types the webhook monitors (e.g., `purchase.created`). Ignored if `all_events` is `true`. |
| `callback`    | String  | URL where the webhook sends POST requests with event data.   |

### Example Response

```json
{
  "results": [
    {
      "type": "webhook",
      "id": "3c90c3cc-0d44-4b50-8888-8dd25736052a",
      "created_on": 1619740800,
      "updated_on": 1619740800,
      "title": "Purchase Webhook",
      "all_events": false,
      "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----",
      "events": ["purchase.created"],
      "callback": "https://example.com/webhook"
    }
  ],
  "next": null,
  "previous": null
}
```

## Notes

- **Purpose**: This endpoint retrieves a list of all webhook rules configured in the CHIP system, including their event configurations and callback URLs.
- **Event Types**: Each webhook’s `events` array lists the specific events it monitors (e.g., `purchase.created`). If `all_events` is `true`, the webhook listens for all available events, and the `events` array is ignored.
- **Test vs. Live**: Test webhooks (created with a test API key) only handle events from test purchases, and live webhooks only handle events from live purchases. They are separate and do not interact.
- **Webhook Payload**: When an event triggers a webhook, the callback URL receives a POST request with the related object’s data (e.g., a Purchase object for `purchase.*` events) and an `event_type` field to identify the event.
- **Public Key**: The `public_key` can be used to verify the authenticity of webhook payloads by checking the signature. It is provided in PEM format if set during webhook creation.
- **Pagination**: The `next` and `previous` fields provide URLs for navigating through paginated results, if applicable. A `null` value indicates no further pages.
- **Security**: Ensure callback URLs are secure (HTTPS) and can handle POST requests. The webhook system does not support CORS, so client-side handling is not recommended.

# Retrieve a Public Key

This endpoint allows you to retrieve the public key used for authenticating webhook or callback payloads in the CHIP system. The key is returned in PEM-encoded RSA format.

## Endpoint

```
GET https://gate.chip-in.asia/api/v1/public_key/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Request Body

This endpoint does not require a request body.

## Example Request

```bash
curl --request GET \
  --url https://gate.chip-in.asia/api/v1/public_key/ \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a PEM-encoded RSA public key as a string in `application/json` format with a status code of 200.

### Response Status

- **200 OK**: The public key was successfully retrieved, returned in `application/json` format.

### Response Attributes

The response is a string containing the PEM-encoded RSA public key used for authenticating webhook or callback payloads.

### Example Response

```json
"-----BEGIN PUBLIC KEY-----\nMIIBojANBgkqhkiG9w0BAQEFAAOCAY8AMIIBigKCAYEA6gLlBKxCB5dxPJbinCzl\nhOfKSgQtOWQQBxmnWIkEVUbqhpnqr3xNYiAvDyMUxYUwuzN44eHO+mR9MZWcSx3c\nbXmKa3gsOzR6GdcOxMGaVxvfje+fujCAlCtO1BP+A9/FS3KcPgCYG1wtAPwA5IAf\nHylL3TsUVIJQFBgiBr6N4Bgapr9eloaFfeYIBRsXmxPKAMJivqxYpLh0V3N4ZFd5\nTGqSEAa4b1ULDr6p0sB2L3QikTdsF0zp03zNceKA6fXDOzD0xWtg9buXvyKwePK4\nM2kcnWBNfsWghrdg0fG3O9bmkaS1oEXydrmJfuiI8h6a64J/1nzooi2yLC9D6Ta0\nS63bbuAHymnQtiNuV7Ixp6IoTGFaS88aTiHaP8rdyWV8JTDFx0qeDzyaGWiYGwEF\nmj/buHCEcRhoajbGkPhYA4YEdn4jy1wZhEr2OMdBPM7mPPI0Hy3hcNJVMVVlrpHe\nIltQATpjlNaJMlRPjbcaiW7dsO3BuF9ZOMGksSOnyYm/AgMBAAE=\n-----END PUBLIC KEY-----"
```

## Notes

- **Purpose**: This endpoint retrieves the public key used to verify the authenticity of webhook or callback payloads sent by the CHIP system.
- **Usage**: The PEM-encoded RSA public key can be used to validate signatures on webhook payloads, ensuring they originate from CHIP and have not been tampered with.
- **Test vs. Live**: The public key retrieved depends on whether a test or live API key is used. Test keys retrieve the public key for test webhooks, and live keys retrieve the public key for live webhooks.
- **Security**: Ensure the key is stored securely and used appropriately for signature verification. The endpoint does not support CORS, so client-side handling is not recommended.

# List of Payment Methods

This endpoint allows you to retrieve a list of payment methods available for a purchase in the CHIP system, filtered by specific query parameters such as `brand_id` and `currency`. The response provides details about available payment methods, their country-specific availability, display names, logos, and card-specific methods.

## Endpoint

```
GET https://gate.chip-in.asia/api/v1/payment_methods/
```

## Authentication

All requests must include an API key obtained from the Developers section of your CHIP account. Use the key as a Bearer token in the Authorization header:

```
Authorization: Bearer <secret_key>
```

**Note**: Make API calls from the server-side to prevent exposing your secret key on the client-side. Cross-Origin Resource Sharing (CORS) is not enabled for security reasons.

## Query Parameters

To retrieve accurate payment methods, provide at least the required `brand_id` and `currency` query parameters, ensuring they match the values used when creating a Purchase. Use the same API key (test or live) that will be used for the Purchase to ensure consistent test_mode settings.

| Parameter         | Type    | Required | Description                                                  |
|-------------------|---------|----------|--------------------------------------------------------------|
| `brand_id`        | String  | Yes      | The UUID of the brand for which to lookup available payment methods, matching `Purchase.brand_id`. |
| `currency`        | String  | Yes      | Currency code in ISO 4217 format (e.g., `MYR`, `EUR`) to be used in the Purchase. |
| `country`         | String  | No       | Country code in ISO 3166-1 alpha-2 format (e.g., `GB`) to filter payment methods by country. |
| `recurring`       | Boolean | No       | If `true` (e.g., `recurring=true`), filters methods that support recurring charges (see `POST /purchases/{id}/charge/`). |
| `skip_capture`    | Boolean | No       | If `true` (e.g., `skip_capture=true`), filters methods that support `skip_capture` functionality (see `Purchase.skip_capture`). |
| `preauthorization`| Boolean | No       | If `true` (e.g., `preauthorization=true`), filters methods that support preauthorization functionality (see `Purchase.skip_capture`). |
| `language`        | String  | No       | Language code in ISO 639-1 format (e.g., `en`) to filter payment methods by language. |
| `amount`          | Integer | No       | Amount in the smallest indivisible units of the currency. Some methods (e.g., FPX) are unavailable for amounts below 1 MYR. |

## Example Request

```bash
curl --request GET \
  --url 'https://gate.chip-in.asia/api/v1/payment_methods/?brand_id=75a76529-91c7-4d98-90a9-8a641d70ee52&currency=MYR' \
  --header 'Authorization: Bearer <secret_key>'
```

## Response

Upon a successful request, the API returns a JSON object containing details about available payment methods with a status code of 200.

### Response Status

- **200 OK**: The list of payment methods was successfully retrieved, returned in `application/json` format.

### Response Attributes

The response is an object with the following attributes:

| Attribute                 | Type   | Description                                                  |
|---------------------------|--------|--------------------------------------------------------------|
| `available_payment_methods`| Array  | List of payment method names available for the Purchase (e.g., `visa`, `mastercard`). These can be used in `payment_method_whitelist` or `?preferred={payment_method}` in `checkout_url`. |
| `by_country`              | Object | Mapping of country codes to available payment methods. Includes an `any` key for methods available in all countries. |
| `country_names`           | Object | Mapping of country codes to their full names (e.g., `GB`: `United Kingdom`, `any`: `Other`). |
| `names`                   | Object | Mapping of payment method names to their display names (e.g., `visa`: `Visa`). |
| `logos`                   | Object | Mapping of payment method names to their logo URLs or list of URLs (e.g., `/static/images/icon-visa.svg`). |
| `card_methods`            | Array  | List of payment methods that are card-based (e.g., `visa`, `american_express`). |

### Example Response

```json
{
  "available_payment_methods": [
    "visa",
    "mastercard",
    "some_method"
  ],
  "by_country": {
    "any": [
      "card"
    ],
    "GB": [
      "some_method"
    ]
  },
  "country_names": {
    "any": "Other",
    "GB": "United Kingdom"
  },
  "names": {
    "visa": "Visa",
    "mastercard": "Mastercard",
    "some_method": "Some method"
  },
  "logos": {
    "some_method": [
      "/static/images/icon-visa.svg",
      "/static/images/icon-mastercard.svg",
      "/static/images/icon-maestro.svg"
    ],
    "visa": "/static/images/icon-visa.svg",
    "mastercard": "/static/images/icon-mastercard.svg"
  },
  "card_methods": [
    "american_express",
    "visa"
  ]
}
```

## Notes

- **Purpose**: This endpoint retrieves a list of payment methods available for a Purchase, filtered by parameters like `brand_id`, `currency`, and optionally `country`, `recurring`, `skip_capture`, `preauthorization`, `language`, or `amount`.
- **Usage**: Use the `available_payment_methods` to populate the `payment_method_whitelist` field in a Purchase or to set a preferred payment method in the `checkout_url` (e.g., `?preferred=visa`). The `card_methods` array identifies card-based methods.
- **Test vs. Live**: The response depends on the API key used (test or live). Test keys return payment methods available for test purchases, and live keys return methods for live purchases.
- **Country Filtering**: The `by_country` object shows which payment methods are available in specific countries, with `any` indicating methods available globally.
- **Amount Restrictions**: Some payment methods (e.g., FPX) may be unavailable for low amounts (e.g., below 1 MYR). Use the `amount` parameter to filter such methods.
- **Logos**: The `logos` object provides URLs for payment method icons, which can be used for display in a checkout interface.
- **Security**: Ensure query parameters match the Purchase configuration to avoid discrepancies. The endpoint does not support CORS, so client-side handling is not recommended.

