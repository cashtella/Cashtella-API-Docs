# Cashtella Partner API

## Overview

The Cashtella Partner API enables businesses to integrate payment processing capabilities into their applications. This API provides endpoints for wallet management, payment processing, transaction history, scheduled payments, and webhook notifications.

## Base URL

```
https://api.cashtella.com/api/v1
```

## Authentication

All API requests require authentication using your Partner API Key in the request headers:

```bash
curl -X GET "https://api.cashtella.com/api/v1/partners/me/test" \
  -H "x-api-key: cpk_your-api-key-here" \
  -H "Content-Type: application/json"
```

**Required Header:**
- `x-api-key`: Your partner API key (starts with `cpk_`)

**Example with different HTTP methods:**
```bash
# GET request
curl -X GET "https://api.cashtella.com/api/v1/partners/me/wallet" \
  -H "x-api-key: cpk_your-api-key-here"

# POST request  
curl -X POST "https://api.cashtella.com/api/v1/partners/me/interac/send" \
  -H "x-api-key: cpk_your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{"amount": 100.00, "receiverEmail": "user@example.com", "receiverName": "John Doe"}'
```

### API Key Scopes

API keys use scopes to control access to different endpoints:

- **`*`** - Full access to all endpoints (default for new API keys)
- **`read`** - Access to all GET endpoints (view data)
- **`write`** - Access to all POST/PUT/DELETE endpoints (modify data)
- **`[]`** - No access (empty array blocks all operations)

**Examples:**
```json
// Full access (default)
{ "scopes": ["*"] }

// Read-only access
{ "scopes": ["read"] }

// Write-only access  
{ "scopes": ["write"] }

// Read and write access
{ "scopes": ["read", "write"] }

// No access
{ "scopes": [] }
```

## API Endpoints

### Authentication

#### Test API Key
```bash
GET /partners/me/test
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Response:**
```json
{
  "result": {
    "success": true,
    "message": "API key is valid",
    "partnerId": "partner-uuid-123",
    "partnerName": "Your Partner Name",
    "scopes": ["*"]
  },
  "statusCode": 200,
  "success": true
}
```

---

### Profile Management

#### Get Partner Profile
```bash
GET /partners/me/profile
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Response:**
```json
{
  "result": {
    "id": "partner-uuid-123",
    "name": "Your Partner Name",
    "email": "partner@example.com",
    "referencePattern": "partner-ref-123",
    "status": "active",
    "createdAt": "2024-01-15T10:30:00.000Z"
  },
  "statusCode": 200,
  "success": true
}
```

#### Update Partner Profile
```bash
PATCH /partners/me/profile
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Updated Partner Name",
  "referencePattern": "new-reference-pattern"
}
```

**Response:**
```json
{
  "result": {
    "id": "partner-uuid-123",
    "name": "Updated Partner Name",
    "email": "partner@example.com",
    "referencePattern": "new-reference-pattern",
    "status": "active",
    "updatedAt": "2024-01-15T10:30:00.000Z"
  },
  "statusCode": 200,
  "success": true
}
```

**Notes:**
- Reference pattern must be unique across all partners. If the pattern is already in use, you'll receive a 409 Conflict error.
- Email updates require admin approval for security reasons. Contact support to change your email address.

---

### Wallet Management

#### Get Wallet Balance
```bash
GET /partners/me/wallet
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Response:**
```json
{
  "result": [
    {
      "id": 77,
      "name": "CAD",
      "currentBalance": "0.00",
      "availableBalance": "0.00",
      "currency": "CAD",
      "accountId": "1234567890",
      "amountIn": 0,
      "amountOut": 0
    }
  ],
  "statusCode": 200,
  "success": true
}
```

---

### Transaction History

#### Get Transactions
```bash
GET /partners/me/transactions?page=1&limit=20&startDate=2024-01-01&endDate=2024-01-31
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)
- `startDate` (optional): Start date filter (YYYY-MM-DD)
- `endDate` (optional): End date filter (YYYY-MM-DD)
- `type` (optional): Transaction type filter (CREDIT, DEBIT)
- `status` (optional): Transaction status filter (PENDING, SUCCESS, FAILED)
- `currency` (optional): Currency filter (CAD, USD, NGN)

**Response:**
```json
{
  "result": {
    "data": [
      {
        "id": 201,
        "createdAt": "2025-09-15T20:41:04.063Z",
        "updatedAt": "2025-09-15T20:41:04.063Z",
        "amount": "2.00",
        "type": "CREDIT",
        "description": "partner-reference-123",
        "status": "SUCCESS",
        "externalTranxId": "TXN123456789",
        "wallet": {
          "name": "CAD",
          "currency": "CAD",
          "accountId": "1234567890"
        }
      }
    ],
    "meta": {
      "page": 1,
      "limit": 20,
      "itemCount": 2,
      "pageCount": 1,
      "hasPreviousPage": false,
      "hasNextPage": false
    }
  },
  "statusCode": 200,
  "success": true
}
```

#### Get Specific Transaction
```bash
GET /partners/me/transactions/:id
```

---

### Payment Processing

#### Send Interac e-Transfer
```bash
POST /partners/me/interac/send
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Request Body:**
```json
{
  "amount": 100.00,
  "receiverEmail": "customer@example.com",
  "receiverName": "John Doe",
  "description": "Payment for services"
}
```

**Response:**
```json
{
  "success": true,
  "transactionId": "txn-uuid-123",
  "apayloTransactionNumber": "APAYLO123456",
  "apayloReferenceNumber": "REF789",
  "referenceNumber": "SEC123",
  "amount": 100.00,
  "receiverEmail": "customer@example.com",
  "message": "Interac transfer initiated successfully"
}
```

#### Get Sent Interac Transfers
```bash
GET /partners/me/interac/sent?page=1&limit=20&startDate=2024-01-01&endDate=2024-01-31
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

#### Get Specific Sent Interac Transfer
```bash
GET /partners/me/interac/sent/:id
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

#### Send Bank Transfer
```bash
POST /partners/me/bank/send
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

**Request Body:**
```json
{
  "amount": 500.00,
  "accountNumber": "1234567890",
  "institutionNumber": "003",
  "transitNumber": "12345",
  "accountHolderName": "John Doe",
  "description": "Payment for services"
}
```

#### Get Sent Bank Transfers
```bash
GET /partners/me/bank/sent?page=1&limit=20&startDate=2024-01-01&endDate=2024-01-31
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

#### Get Specific Sent Bank Transfer
```bash
GET /partners/me/bank/sent/:id
```

**Headers:**
```bash
x-api-key: cpk_your-api-key-here
Content-Type: application/json
```

---

### Scheduled Payments

#### Create Scheduled Payment
```bash
POST /partners/me/scheduled-payments
```

**Request Body:**
```json
{
  "amount": 300.00,
  "description": "Monthly salary payment",
  "frequency": "MONTHLY",
  "startDate": "2024-02-01",
  "startTime": "09:00",
  "totalExecutions": 12,
  "paymentMethod": "INTERAC_E_TRANSFER",
  "receiverEmail": "employee@company.com",
  "receiverName": "Jane Smith"
}
```

**Frequency Options:**
- `DAILY`
- `WEEKLY`
- `MONTHLY`
- `QUARTERLY`
- `YEARLY`

**Payment Method Options:**
- `INTERAC_E_TRANSFER`
- `BANK_TRANSFER`

**Response:**
```json
{
  "id": "sched-uuid-789",
  "amount": 300.00,
  "description": "Monthly salary payment",
  "frequency": "MONTHLY",
  "startDate": "2024-02-01T09:00:00.000Z",
  "nextExecutionDate": "2024-02-01T09:00:00.000Z",
  "totalExecutions": 12,
  "executedCount": 0,
  "isActive": true,
  "paymentMethod": "INTERAC_E_TRANSFER",
  "status": "ACTIVE",
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

#### Get Scheduled Payments
```bash
GET /partners/me/scheduled-payments?page=1&limit=20
```

#### Get Specific Scheduled Payment
```bash
GET /partners/me/scheduled-payments/:id
```

#### Update Scheduled Payment
```bash
PUT /partners/me/scheduled-payments/:id
```

#### Delete Scheduled Payment
```bash
DELETE /partners/me/scheduled-payments/:id
```

---

### Webhook Management

#### Create Webhook
```bash
POST /partners/me/webhooks
```

**Request Body:**
```json
{
  "url": "https://your-app.com/webhooks/cashtella",
  "secret": "your-webhook-secret",
  "events": ["payment.received", "payment.sent", "scheduled_payment.executed"],
  "isActive": true,
  "maxRetries": 3,
  "timeoutMs": 5000
}
```

**Available Events:**
- `payment.received` - When money is received
- `payment.sent` - When money is sent
- `scheduled_payment.executed` - When scheduled payment runs
- `scheduled_payment.failed` - When scheduled payment fails

**Response:**
```json
{
  "id": "webhook-uuid-123",
  "url": "https://your-app.com/webhooks/cashtella",
  "events": ["payment.received", "payment.sent", "scheduled_payment.executed"],
  "isActive": true,
  "maxRetries": 3,
  "timeoutMs": 5000,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

#### Get Webhooks
```bash
GET /partners/me/webhooks
```

#### Get Specific Webhook
```bash
GET /partners/me/webhooks/:id
```

#### Update Webhook
```bash
PUT /partners/me/webhooks/:id
```

#### Delete Webhook
```bash
DELETE /partners/me/webhooks/:id
```

#### Test Webhook
```bash
POST /partners/me/webhooks/:id/test
```

**Response:**
```json
{
  "success": true,
  "message": "Test webhook sent successfully",
  "data": {
    "deliveryId": "delivery-uuid-123",
    "status": "pending"
  }
}
```

#### Get Webhook Deliveries
```bash
GET /partners/me/webhooks/:id/deliveries?page=1&limit=20
```

---

## Webhook Events

### Payment Received
```json
{
  "event": "payment.received",
  "data": {
    "transactionId": "txn-uuid-123",
    "amount": 100.00,
    "currency": "CAD",
    "paymentMethod": "INTERAC_E_TRANSFER",
    "description": "Interac eTransfer funding",
    "externalTransactionId": "APAYLO123456",
    "senderEmail": "sender@example.com",
    "senderName": "John Doe",
    "walletId": "wallet-uuid-123",
    "timestamp": "2024-01-15T10:30:00.000Z"
  },
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

### Payment Sent
```json
{
  "event": "payment.sent",
  "data": {
    "transactionId": "txn-uuid-456",
    "amount": 50.00,
    "currency": "CAD",
    "paymentMethod": "INTERAC_E_TRANSFER",
    "description": "Interac ETransfer to customer",
    "externalTransactionId": "APAYLO789012",
    "receiverEmail": "customer@example.com",
    "receiverName": "Jane Smith",
    "walletId": "wallet-uuid-123",
    "referenceNumber": "SEC123",
    "timestamp": "2024-01-15T10:30:00.000Z"
  },
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

### Scheduled Payment Executed
```json
{
  "event": "scheduled_payment.executed",
  "data": {
    "scheduledPaymentId": "sched-uuid-789",
    "transactionId": "txn-uuid-101",
    "amount": 300.00,
    "currency": "CAD",
    "paymentMethod": "INTERAC_E_TRANSFER",
    "description": "Monthly salary payment",
    "frequency": "MONTHLY",
    "executionCount": 3,
    "totalExecutions": 12,
    "nextExecutionDate": "2024-02-15T10:30:00.000Z",
    "status": "ACTIVE",
    "walletId": "wallet-uuid-123",
    "timestamp": "2024-01-15T10:30:00.000Z"
  },
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

## Webhook Security

All webhook requests are signed with HMAC-SHA256 signatures for security verification. The signature is included in the `X-Cashtella-Signature` header.

### Signature Verification (Node.js)
```javascript
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  
  return crypto.timingSafeEqual(
    Buffer.from(signature, 'hex'),
    Buffer.from(expectedSignature, 'hex')
  );
}
```

## Error Handling

### Common Error Responses

```json
{
  "success": false,
  "statusCode": 400,
  "message": "Validation failed",
  "errors": [
    "amount must be greater than 0",
    "receiverEmail must be a valid email"
  ]
}
```

### HTTP Status Codes

- `200` - Success
- `201` - Created
- `400` - Bad Request (validation error)
- `401` - Unauthorized (invalid API key)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found
- `429` - Too Many Requests (rate limited)
- `500` - Internal Server Error

## Rate Limits

- **Standard endpoints**: 1000 requests per hour
- **Payment endpoints**: 100 requests per hour
- **Webhook endpoints**: 100 requests per hour

## Code Examples

### Node.js Example
```javascript
const axios = require('axios');

class CashtellaAPI {
  constructor(apiKey, baseURL = 'https://api.cashtella.com/api/v1') {
    this.apiKey = apiKey;
    this.baseURL = baseURL;
  }

  async request(method, endpoint, data = null) {
    try {
      const response = await axios({
        method,
        url: `${this.baseURL}${endpoint}`,
        headers: {
          'x-api-key': this.apiKey,
          'Content-Type': 'application/json'
        },
        data
      });
      return response.data;
    } catch (error) {
      throw new Error(`API Error: ${error.response?.data?.message || error.message}`);
    }
  }

  // Get wallet balance
  async getWallet() {
    return this.request('GET', '/partners/me/wallet');
  }

  // Send Interac transfer
  async sendInterac(amount, receiverEmail, receiverName, description) {
    return this.request('POST', '/partners/me/interac/send', {
      amount,
      receiverEmail,
      receiverName,
      description
    });
  }

  // Get transactions
  async getTransactions(page = 1, limit = 20, filters = {}) {
    const params = new URLSearchParams({ page, limit, ...filters });
    return this.request('GET', `/partners/me/transactions?${params}`);
  }
}

// Usage
const api = new CashtellaAPI('your-api-key');

// Get wallet balance
const wallet = await api.getWallet();
console.log('Available balance:', wallet[0].availableBalance);

// Send payment
const result = await api.sendInterac(
  100.00,
  'customer@example.com',
  'John Doe',
  'Payment for services'
);
console.log('Payment sent:', result.transactionId);
```

### PHP Example
```php
<?php

class CashtellaAPI {
    private $apiKey;
    private $baseURL;

    public function __construct($apiKey, $baseURL = 'https://api.cashtella.com/api/v1') {
        $this->apiKey = $apiKey;
        $this->baseURL = $baseURL;
    }

    private function request($method, $endpoint, $data = null) {
        $url = $this->baseURL . $endpoint;
        
        $headers = [
            'x-api-key: ' . $this->apiKey,
            'Content-Type: application/json'
        ];

        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, $method);
        
        if ($data) {
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
        }

        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);

        if ($httpCode >= 400) {
            throw new Exception('API Error: ' . $response);
        }

        return json_decode($response, true);
    }

    public function getWallet() {
        return $this->request('GET', '/partners/me/wallet');
    }

    public function sendInterac($amount, $receiverEmail, $receiverName, $description) {
        return $this->request('POST', '/partners/me/interac/send', [
            'amount' => $amount,
            'receiverEmail' => $receiverEmail,
            'receiverName' => $receiverName,
            'description' => $description
        ]);
    }
}

// Usage
$api = new CashtellaAPI('your-api-key');

// Get wallet balance
$wallet = $api->getWallet();
echo "Available balance: " . $wallet[0]['availableBalance'];

// Send payment
$result = $api->sendInterac(
    100.00,
    'customer@example.com',
    'John Doe',
    'Payment for services'
);
echo "Payment sent: " . $result['transactionId'];
?>
```

---

## Getting Started

1. **Contact Cashtella** to obtain your Partner API Key
2. **Test your API key** using the `/partners/me/test` endpoint
3. **Check your wallet balance** using `/partners/me/wallet`
4. **Set up webhooks** for real-time notifications
5. **Start processing payments** using the payment endpoints

## Support

For API support:
- **Email**: api-support@cashtella.com
- **Documentation**: This file
- **Status Page**: https://status.cashtella.com

---

**Version**: 1.0  
**Last Updated**: January 2024
