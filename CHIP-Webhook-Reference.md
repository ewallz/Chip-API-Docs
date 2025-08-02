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

