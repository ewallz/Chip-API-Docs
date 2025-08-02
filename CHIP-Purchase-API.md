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


