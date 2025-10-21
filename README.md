# Cashtella Partner API Documentation

## Overview

The Cashtella Partner API allows businesses to integrate payment processing, wallet management, and automated transfers into their applications. This API provides secure access to Cashtella's financial services through API key authentication.

## Base URL

```
https://api.cashtella.com/api/v1
```

## Authentication

All API requests require an API key in the request header:

```http
x-api-key: cbk_your_api_key_here
```

### Getting an API Key

1. Create a business account on the Cashtella dashboard
2. Enable API access for your account (contact support)
3. Generate API keys through the dashboard
4. Use the API key in all requests

## API Key Format

```
cbk_[uuid]_[hash]
```

Example: `cbk_12345678-1234-1234-1234-123456789abc_abcdef123456`

## Response Format

All API responses follow this structure:

```json
{
  "result": { ... },
  "statusCode": 200,
  "success": true
}
```

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "statusCode": 400,
  "message": "Error description",
  "errors": ["Detailed error messages"]
}
```

### Common HTTP Status Codes

- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized (Invalid API key)
- `404` - Not Found
- `409` - Conflict
- `500` - Internal Server Error

---

## API Endpoints

### 🔍 **Profile Management**

#### Test API Key
```http
GET /partners/me/test
```

**Response:**
```json
{
  "result": {
    "success": true,
    "message": "API key is valid",
    "userId": 123,
    "businessName": "My Business Inc.",
    "scopes": ["*"]
  },
  "statusCode": 200,
  "success": true
}
```

#### Get Business Profile
```http
GET /partners/me/profile
```

**Response:**
```json
{
  "result": {
    "result": {
      "id": 123,
      "businessName": "My Business Inc.",
      "email": "business@example.com",
      "referencePattern": "business-ref-123",
      "hasApiAccess": true,
      "createdAt": "2024-01-15T10:30:00.000Z"
    },
    "statusCode": 200,
    "success": true
  },
  "statusCode": 200,
  "success": true
}
```

---

### 💰 **Wallet Management**

#### Get Business Wallets
```http
GET /partners/me/wallet
```

**Response:**
```json
{
  "result": [
    {
      "id": "wallet-uuid-123",
      "currency": "CAD",
      "availableBalance": "1200.00",
      "currentBalance": "1500.00",
      "amountIn": "2000.00",
      "amountOut": "500.00",
      "name": "CAD",
      "accountId": "acc_123456",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "updatedAt": "2024-01-15T10:30:00.000Z"
    }
  ],
  "statusCode": 200,
  "success": true
}
```

---

### 📊 **Transaction History**

#### Get Transaction History
```http
GET /partners/me/transactions?page=1&limit=20&startDate=2024-01-01&endDate=2024-01-31&type=CREDIT&status=SUCCESS&currency=CAD
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)
- `startDate` (optional): Start date filter (YYYY-MM-DD)
- `endDate` (optional): End date filter (YYYY-MM-DD)
- `type` (optional): Transaction type (`CREDIT`, `DEBIT`)
- `status` (optional): Transaction status (`PENDING`, `SUCCESS`, `FAILED`)
- `currency` (optional): Currency filter (`CAD`, `USD`, `NGN`)

**Response:**
```json
{
  "result": {
    "data": [
      {
        "id": "txn-uuid-123",
        "amount": 100.00,
        "type": "CREDIT",
        "status": "SUCCESS",
        "description": "Interac eTransfer funding",
        "currency": "CAD",
        "externalTranxId": "APAYLO123456",
        "createdAt": "2024-01-15T10:30:00.000Z",
        "metas": {}
      }
    ],
    "meta": {
      "page": 1,
      "limit": 20,
      "itemCount": 150,
      "pageCount": 8,
      "hasPreviousPage": false,
      "hasNextPage": true
    }
  },
  "statusCode": 200,
  "success": true
}
```

#### Get Specific Transaction
```http
GET /partners/me/transactions/{transactionId}
```

**Response:**
```json
{
  "result": {
    "id": "txn-uuid-123",
    "amount": 100.00,
    "type": "CREDIT",
    "status": "SUCCESS",
    "description": "Interac eTransfer funding",
    "currency": "CAD",
    "externalTranxId": "APAYLO123456",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "metas": {}
  },
  "statusCode": 200,
  "success": true
}
```

---

### 💸 **Interac e-Transfer**

#### Send Interac e-Transfer
```http
POST /partners/me/interac/send
```

**Request Body:**
```json
{
  "amount": 100.00,
  "receiverEmail": "recipient@example.com",
  "message": "Payment for services",
  "reference": "PAY-12345"
}
```

**Response:**
```json
{
  "result": {
    "success": true,
    "transactionId": "txn-uuid-123",
    "apayloTransactionNumber": "APAYLO123456",
    "apayloReferenceNumber": "REF789",
    "referenceNumber": "SEC123",
    "amount": 100.00,
    "receiverEmail": "recipient@example.com",
    "message": "Interac transfer initiated successfully"
  },
  "statusCode": 201,
  "success": true
}
```

#### Get Sent Interac Transfers
```http
GET /partners/me/interac/sent?startDate=2024-01-01&endDate=2024-01-31&page=1&limit=20
```

**Response:**
```json
{
  "result": {
    "data": [
      {
        "id": "transfer-uuid-123",
        "amount": 100.00,
        "receiverEmail": "recipient@example.com",
        "status": "SUCCESS",
        "referenceNumber": "SEC123",
        "createdAt": "2024-01-15T10:30:00.000Z"
      }
    ],
    "meta": {
      "page": 1,
      "limit": 20,
      "itemCount": 50,
      "pageCount": 3,
      "hasPreviousPage": false,
      "hasNextPage": true
    }
  },
  "statusCode": 200,
  "success": true
}
```

#### Get Specific Interac Transfer
```http
GET /partners/me/interac/sent/{transferId}
```

---

### 🏦 **Bank Transfers**

#### Send Bank Transfer
```http
POST /partners/me/bank/send
```

**Request Body:**
```json
{
  "amount": 500.00,
  "bankCode": "001",
  "accountNumber": "1234567890",
  "accountName": "John Doe",
  "reference": "BANK-12345"
}
```

#### Get Sent Bank Transfers
```http
GET /partners/me/bank/sent?startDate=2024-01-01&endDate=2024-01-31&page=1&limit=20
```

#### Get Specific Bank Transfer
```http
GET /partners/me/bank/sent/{transferId}
```

---

### ⏰ **Scheduled Payments**

#### Create Scheduled Payment
```http
POST /partners/me/scheduled-payments
```

**Request Body:**
```json
{
  "amount": 100.50,
  "frequency": "MONTHLY",
  "startDate": "2024-01-01",
  "startTime": "09:00",
  "paymentMethod": "INTERAC_E_TRANSFER",
  "receiverEmail": "receiver@example.com",
  "receiverName": "John Doe",
  "description": "Monthly rent payment"
}
```

**Required Fields:**
- `amount` - Payment amount
- `frequency` - How often to pay (`DAILY`, `WEEKLY`, `MONTHLY`, `ONE_TIME`)
- `startDate` - When to start
- `paymentMethod` - How to pay (`INTERAC_E_TRANSFER`, `BANK_TRANSFER`)
- `receiverEmail` - Who to pay (for Interac)
- `receiverName` - Recipient name (for Interac)

**Optional Fields:**
- `startTime` - What time to pay (format: "09:00")
- `endDate` - When to stop (for recurring payments)
- `description` - Payment description

**Response:**
```json
{
  "result": {
    "id": 123,
    "amount": 100.50,
    "currency": "CAD",
    "frequency": "MONTHLY",
    "startDate": "2024-01-01T00:00:00.000Z",
    "nextExecutionDate": "2024-02-01T09:00:00.000Z",
    "paymentMethod": "INTERAC_E_TRANSFER",
    "status": "ACTIVE",
    "isActive": true,
    "createdAt": "2024-01-15T10:30:00.000Z"
  },
  "statusCode": 201,
  "success": true
}
```

#### Get Scheduled Payments
```http
GET /partners/me/scheduled-payments?status=ACTIVE&page=1&limit=20
```

**Query Parameters:**
- `status` (optional): Filter by status (`ACTIVE`, `PAUSED`, `COMPLETED`, `FAILED`)
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)

#### Get Specific Scheduled Payment
```http
GET /partners/me/scheduled-payments/{paymentId}
```

#### Update Scheduled Payment
```http
PUT /partners/me/scheduled-payments/{paymentId}
```

#### Delete Scheduled Payment
```http
DELETE /partners/me/scheduled-payments/{paymentId}
```

#### Get Scheduled Payment Executions
```http
GET /partners/me/scheduled-payments/{paymentId}/executions?page=1&limit=20
```

**Response:**
```json
{
  "result": [
    {
      "id": "execution-uuid-123",
      "executionDate": "2024-01-01T09:00:00.000Z",
      "status": "COMPLETED",
      "amount": 100.50,
      "currency": "CAD",
      "transactionId": "txn-uuid-123",
      "executionMetadata": {
        "apaylo_transaction_number": "APAYLO123456",
        "payment_method": "INTERAC_E_TRANSFER"
      }
    }
  ],
  "statusCode": 200,
  "success": true
}
```

---

### 🔔 **Webhooks**

**Note:** Webhooks are managed through the Cashtella dashboard. Use the dashboard to create, update, and manage your webhooks.

#### Get Webhooks
```http
GET /partners/me/webhooks
```

#### Get Specific Webhook
```http
GET /partners/me/webhooks/{webhookId}
```

#### Get Webhook Deliveries
```http
GET /partners/me/webhooks/{webhookId}/deliveries?page=1&limit=20
```

#### Test Webhook
```http
POST /partners/me/webhooks/{webhookId}/test
```

---

## Webhook Events

### Event Types

- `payment.received` - Payment received in wallet
- `payment.sent` - Payment sent from wallet
- `scheduled_payment.executed` - Scheduled payment executed
- `scheduled_payment.failed` - Scheduled payment failed
- `webhook.test` - Test webhook event

### Webhook Payload Format

```json
{
  "event": "payment.received",
  "data": {
    "transactionId": "txn-uuid-123",
    "amount": 100.00,
    "currency": "CAD",
    "type": "CREDIT",
    "description": "Interac eTransfer funding"
  },
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

### Webhook Security

All webhooks include an HMAC signature for verification:

```http
X-Cashtella-Signature: sha256=abc123...
```

---

## Rate Limits

- **Standard**: 1000 requests per hour per API key
- **Burst**: 100 requests per minute
- **Webhooks**: 10 webhook deliveries per second

---

## SDKs and Examples

### cURL Example

```bash
curl -X GET "https://api.cashtella.com/api/v1/partners/me/profile" \
  -H "x-api-key: cbk_your_api_key_here" \
  -H "Content-Type: application/json"
```

### JavaScript Example

```javascript
const response = await fetch('https://api.cashtella.com/api/v1/partners/me/profile', {
  method: 'GET',
  headers: {
    'x-api-key': 'cbk_your_api_key_here',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data.result);
```

### Python Example

```python
import requests

headers = {
    'x-api-key': 'cbk_your_api_key_here',
    'Content-Type': 'application/json'
}

response = requests.get(
    'https://api.cashtella.com/api/v1/partners/me/profile',
    headers=headers
)

data = response.json()
print(data['result'])
```

---

## Support

For API support and questions:
- Email: api-support@cashtella.com
- Documentation: https://docs.cashtella.com
- Status Page: https://status.cashtella.com

---

## Changelog

### Version 1.0.0
- Initial release of Partner API
- Support for wallet management, transactions, Interac e-Transfer, bank transfers, scheduled payments, and webhooks
