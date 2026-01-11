# TikTok API Documentation - Cadimar Agency Platform

## General Information

- **Base URL**: `https://app.cadimar.net`
- **API Prefix**: `/api/dealer`
- **Authentication**: API Key via `X-API-Key` header
- **Content-Type**: `application/json`

## Authentication

All API endpoints require a valid API Key in the header:

```
X-API-Key: your-api-key-here
```

### Authentication Failure Response

```json
{
  "code": "403",
  "message": "Invalid API Key"
}
```

---

## 1. Get TikTok Accounts List

Endpoint to retrieve all TikTok accounts belonging to the dealer.

### Request

```http
GET /api/dealer/tiktok/accounts
```

**Headers:**
```
X-API-Key: your-api-key-here
```

### Success Response (200 OK)

```json
{
  "code": "200",
  "message": "TikTok accounts fetched successfully",
  "data": [
    {
      "id": 1,
      "uid": "7234567890123456789",
      "status": "Active",
      "balance": 1000.50,
      "account": "TikTok Account Name",
      "validBalance": 950.00,
      "totalMoney": 5000.00,
      "timezone": "UTC+07:00",
      "autoDepositEnabled": true,
      "autoDepositThreshold": 100.00,
      "autoDepositAmount": 500.00,
      "note": "Account note",
      "partners": [
        {"idbc": "123456789"},
        {"idbc": "987654321"}
      ]
    },
    {
      "id": 2,
      "uid": "7345678901234567890",
      "status": "Disabled",
      "balance": 250.00,
      "account": "Second TikTok Account",
      "validBalance": 200.00,
      "totalMoney": 1500.00,
      "timezone": "UTC+07:00",
      "autoDepositEnabled": false,
      "autoDepositThreshold": 50.00,
      "autoDepositAmount": 200.00,
      "note": "Secondary account",
      "partners": [
        {"idbc": "555666777"}
      ]
    }
  ]
}
```

### Data Fields

| Field | Type | Description |
|--------|------|-------|
| `id` | Long | Internal account ID |
| `uid` | String | TikTok account UID |
| `status` | String | Account status (Active, Disabled, Unknown) |
| `balance` | BigDecimal | Account balance |
| `account` | String | Account name |
| `validBalance` | BigDecimal | Available balance |
| `totalMoney` | BigDecimal | Total amount deposited |
| `timezone` | String | Timezone in UTC format (e.g., "UTC+07:00", "UTC-05:00") |
| `autoDepositEnabled` | Boolean | Auto-deposit enabled/disabled |
| `autoDepositThreshold` | BigDecimal | Auto-deposit trigger threshold |
| `autoDepositAmount` | BigDecimal | Amount to auto-deposit per transaction |
| `note` | String | User note |
| `partners` | Array[Object] | List of linked Business Centers, each object contains {"idbc": "id"} |

### Error Response (403 Forbidden)

```json
{
  "code": "403",
  "message": "Invalid API Key"
}
```

### Usage Examples

**cURL:**
```bash
curl -X GET "https://app.cadimar.net/api/dealer/tiktok/accounts" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json"
```

**JavaScript (Axios):**
```javascript
const axios = require('axios');

axios.get('https://app.cadimar.net/api/dealer/tiktok/accounts', {
  headers: {
    'X-API-Key': 'your-api-key-here'
  }
})
.then(response => {
  console.log('Accounts:', response.data.data);
})
.catch(error => {
  console.error('Error:', error.response.data);
});
```

**Python (Requests):**
```python
import requests

headers = {
    'X-API-Key': 'your-api-key-here'
}

response = requests.get(
    'https://app.cadimar.net/api/dealer/tiktok/accounts',
    headers=headers
)

if response.status_code == 200:
    accounts = response.json()['data']
    print(f"Found {len(accounts)} accounts")
else:
    print(f"Error: {response.json()['message']}")
```

---

## 2. Deposit/Withdraw from TikTok Account

Endpoint to perform deposit or withdrawal transactions on a TikTok account.

### Request

```http
POST /api/dealer/tiktok/accounts/{uid}/transaction
```

**Path Parameters:**
- `uid` (String, required): TikTok account UID

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Body:**
```json
{
  "amount": 100.00,
  "type": "Top-up"
}
```

### Request Parameters

| Field | Type | Required | Description |
|--------|------|----------|-------|
| `amount` | BigDecimal | Yes | Transaction amount (must be > 0) |
| `type` | String | Yes | Transaction type: "Top-up" or "Withdraw" |

### Success Response - Top-up (200 OK)

```json
{
  "code": "200",
  "status": "SUCCESS",
  "message": "Top-up successful"
}
```

### Success Response - Withdraw (200 OK)

```json
{
  "code": "200",
  "status": "SUCCESS",
  "message": "Withdrawal successful"
}
```

### Error Response - Insufficient Balance (400 Bad Request)

```json
{
  "code": "400",
  "status": "FAILED",
  "message": "Insufficient balance in your wallet"
}
```

### Error Response - Account Not Found (404 Not Found)

```json
{
  "code": "404",
  "message": "Account not found or does not belong to the dealer."
}
```

### Error Response - Invalid Amount (400 Bad Request)

```json
{
  "code": "400",
  "message": "Invalid or missing 'amount'."
}
```

### Error Response - Invalid Type (400 Bad Request)

```json
{
  "code": "400",
  "message": "Transaction 'type' must be 'Top-up' or 'Withdraw'."
}
```

### Pending Response - Processing (202 Accepted)

```json
{
  "code": "202",
  "status": "PENDING",
  "message": "Transaction is being processed"
}
```

### Server Error Response (500 Internal Server Error)

```json
{
  "code": "500",
  "status": "ERROR",
  "message": "An unexpected server error occurred."
}
```

### Usage Examples

**cURL - Deposit:**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/transaction" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 100.00,
    "type": "Top-up"
  }'
```

**cURL - Withdraw:**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/transaction" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 50.00,
    "type": "Withdraw"
  }'
```

**JavaScript (Axios):**
```javascript
const axios = require('axios');

// Deposit
axios.post(
  'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/transaction',
  {
    amount: 100.00,
    type: 'Top-up'
  },
  {
    headers: {
      'X-API-Key': 'your-api-key-here',
      'Content-Type': 'application/json'
    }
  }
)
.then(response => {
  console.log('Success:', response.data.message);
})
.catch(error => {
  console.error('Error:', error.response.data);
});
```

**Python (Requests):**
```python
import requests

headers = {
    'X-API-Key': 'your-api-key-here',
    'Content-Type': 'application/json'
}

# Deposit
data = {
    'amount': 100.00,
    'type': 'Top-up'
}

response = requests.post(
    'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/transaction',
    json=data,
    headers=headers
)

if response.status_code == 200:
    print(f"Success: {response.json()['message']}")
else:
    print(f"Error: {response.json()['message']}")
```

### Notes

- The `amount` must be a positive number (> 0)
- Type only accepts 2 values: `"Top-up"` or `"Withdraw"`
- For withdrawals, the dealer's wallet balance must be sufficient
- The TikTok account must belong to the dealer making the API call
- All transactions via API will be marked with method "API" in the system

---

## 3. Configure Auto-Deposit for TikTok Account

Endpoint to configure automatic deposit settings for a TikTok account. The system will automatically add funds when the balance falls below the specified threshold.

### Request

```http
POST /api/dealer/tiktok/accounts/{uid}/auto-deposit
```

**Path Parameters:**
- `uid` (String, required): TikTok account UID

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Body:**
```json
{
  "enabled": true,
  "threshold": 100.00,
  "amount": 500.00,
  "mode": 2,
  "maxTimes": 10,
  "totalAmount": null
}
```

### Request Parameters

| Field | Type | Required | Description |
|--------|------|----------|-------|
| `enabled` | Boolean | Yes | Enable/disable auto-deposit |
| `threshold` | BigDecimal | Yes | Minimum balance to trigger auto-deposit |
| `amount` | BigDecimal | Yes | Amount to deposit each time (must be > 0) |
| `mode` | Integer | Yes | Auto-deposit mode: 1 (unlimited), 2 (max times), 3 (total amount) |
| `maxTimes` | Integer | Conditional | Required if mode = 2. Maximum number of auto-deposits allowed |
| `totalAmount` | BigDecimal | Conditional | Required if mode = 3. Total amount limit for all auto-deposits |

### Auto-Deposit Modes

| Mode | Description | Required Fields |
|------|-------------|----------------|
| 1 | **Unlimited** - Auto-deposit will continue indefinitely | `enabled`, `threshold`, `amount`, `mode` |
| 2 | **Max Times** - Auto-deposit up to N times | `enabled`, `threshold`, `amount`, `mode`, `maxTimes` |
| 3 | **Total Amount** - Auto-deposit until total reaches specified amount | `enabled`, `threshold`, `amount`, `mode`, `totalAmount` |

### Success Response (200 OK)

```json
{
  "code": "200",
  "message": "Auto-deposit settings updated successfully",
  "data": {
    "uid": "7234567890123456789",
    "autoDepositEnabled": true,
    "autoDepositThreshold": 100.00,
    "autoDepositAmount": 500.00,
    "autoDepositMode": 2,
    "autoDepositMaxTimes": 10,
    "remainingTopupTimes": 10
  }
}
```

### Error Responses

**Account Not Found (404 Not Found)**
```json
{
  "code": "404",
  "message": "Account not found or does not belong to the dealer."
}
```

**Invalid Threshold (400 Bad Request)**
```json
{
  "code": "400",
  "message": "Threshold must be greater than or equal to 0"
}
```

**Invalid Amount (400 Bad Request)**
```json
{
  "code": "400",
  "message": "Deposit amount must be greater than 0"
}
```

**Invalid Mode (400 Bad Request)**
```json
{
  "code": "400",
  "message": "Mode must be 1 (unlimited), 2 (max times), or 3 (total amount)"
}
```

**Missing maxTimes (400 Bad Request)**
```json
{
  "code": "400",
  "message": "maxTimes is required when mode is 2"
}
```

**Invalid maxTimes (400 Bad Request)**
```json
{
  "code": "400",
  "message": "maxTimes must be greater than 0"
}
```

**Missing totalAmount (400 Bad Request)**
```json
{
  "code": "400",
  "message": "totalAmount is required when mode is 3"
}
```

**Invalid totalAmount (400 Bad Request)**
```json
{
  "code": "400",
  "message": "totalAmount must be greater than 0"
}
```

### Usage Examples

**cURL - Mode 1 (Unlimited):**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "enabled": true,
    "threshold": 50.00,
    "amount": 200.00,
    "mode": 1
  }'
```

**cURL - Mode 2 (Max Times):**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "enabled": true,
    "threshold": 100.00,
    "amount": 500.00,
    "mode": 2,
    "maxTimes": 10
  }'
```

**cURL - Mode 3 (Total Amount):**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "enabled": true,
    "threshold": 100.00,
    "amount": 500.00,
    "mode": 3,
    "totalAmount": 5000.00
  }'
```

**cURL - Disable Auto-Deposit:**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "enabled": false,
    "threshold": 0,
    "amount": 0,
    "mode": 1
  }'
```

**JavaScript (Axios) - Mode 2:**
```javascript
const axios = require('axios');

axios.post(
  'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit',
  {
    enabled: true,
    threshold: 100.00,
    amount: 500.00,
    mode: 2,
    maxTimes: 10
  },
  {
    headers: {
      'X-API-Key': 'your-api-key-here',
      'Content-Type': 'application/json'
    }
  }
)
.then(response => {
  console.log('Auto-deposit configured:', response.data.data);
})
.catch(error => {
  console.error('Error:', error.response.data);
});
```

**Python (Requests) - Mode 3:**
```python
import requests

headers = {
    'X-API-Key': 'your-api-key-here',
    'Content-Type': 'application/json'
}

data = {
    'enabled': True,
    'threshold': 100.00,
    'amount': 500.00,
    'mode': 3,
    'totalAmount': 5000.00
}

response = requests.post(
    'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/auto-deposit',
    json=data,
    headers=headers
)

if response.status_code == 200:
    result = response.json()['data']
    print(f"Auto-deposit enabled: {result['autoDepositEnabled']}")
    print(f"Threshold: ${result['autoDepositThreshold']}")
    print(f"Amount per deposit: ${result['autoDepositAmount']}")
else:
    print(f"Error: {response.json()['message']}")
```

### How Auto-Deposit Works

1. **Monitoring**: The system checks account balances periodically
2. **Trigger**: When balance falls below `threshold`, auto-deposit is triggered
3. **Execution**: System deposits `amount` from dealer's wallet to the account
4. **Mode Enforcement**:
   - **Mode 1**: Continues indefinitely
   - **Mode 2**: Stops after `maxTimes` deposits
   - **Mode 3**: Stops when total deposited reaches `totalAmount`
5. **Notification**: Transaction is logged in the system

### Important Notes

- Auto-deposit requires sufficient balance in the dealer's wallet
- If dealer's wallet balance is insufficient, auto-deposit will fail and retry later
- The TikTok account must belong to the dealer making the API call
- Threshold must be >= 0 (0 means always trigger when account has any spending)
- Amount must be > 0
- For mode 2: `maxTimes` must be > 0
- For mode 3: `totalAmount` must be > 0 and should be >= `amount`
- When disabled, all settings are preserved but auto-deposit will not execute
- Changing mode will reset `remainingTopupTimes` accordingly

---

## 4. Add Partner to TikTok Account

Endpoint to add a Business Center (BC) partner to a TikTok account.

### Request

```http
POST /api/dealer/tiktok/accounts/{uid}/partners
```

**Path Parameters:**
- `uid` (String, required): TikTok account UID

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Body:**
```json
{
  "idbc": "123456789"
}
```

### Request Parameters

| Field | Type | Required | Description |
|--------|------|----------|-------|
| `idbc` | String | Yes | Business Center ID to add |

### Success Response (200 OK)

```json
{
  "code": "200",
  "message": "Partner added successfully",
  "data": {
    "idbc": "123456789"
  }
}
```

### Error Response - Account Not Found (404 Not Found)

```json
{
  "code": "404",
  "message": "Account not found"
}
```

### Error Response - Missing IDBC (400 Bad Request)

```json
{
  "code": "400",
  "message": "Missing partner IDBC"
}
```

### Error Response - Partner Exists or Other Error (400 Bad Request)

```json
{
  "code": "400",
  "message": "Error message from service"
}
```

### Usage Examples

**cURL:**
```bash
curl -X POST "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners" \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "idbc": "123456789"
  }'
```

**JavaScript (Axios):**
```javascript
const axios = require('axios');

axios.post(
  'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners',
  {
    idbc: '123456789'
  },
  {
    headers: {
      'X-API-Key': 'your-api-key-here',
      'Content-Type': 'application/json'
    }
  }
)
.then(response => {
  console.log('Partner added:', response.data.data.idbc);
})
.catch(error => {
  console.error('Error:', error.response.data);
});
```

**Python (Requests):**
```python
import requests

headers = {
    'X-API-Key': 'your-api-key-here',
    'Content-Type': 'application/json'
}

data = {
    'idbc': '123456789'
}

response = requests.post(
    'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners',
    json=data,
    headers=headers
)

if response.status_code == 200:
    print(f"Partner added: {response.json()['data']['idbc']}")
else:
    print(f"Error: {response.json()['message']}")
```

### Notes

- IDBC cannot be empty
- The TikTok account must belong to the dealer making the API call
- Partners will have "Approved" status by default after being added
- Cannot add duplicate partners

---

## 5. Remove Partner from TikTok Account

Endpoint to remove a Business Center partner from a TikTok account.

### Request

```http
DELETE /api/dealer/tiktok/accounts/{uid}/partners/{idbc}
```

**Path Parameters:**
- `uid` (String, required): TikTok account UID
- `idbc` (String, required): Business Center ID to remove

**Headers:**
```
X-API-Key: your-api-key-here
```

### Success Response (200 OK)

```json
{
  "code": "200",
  "message": "Partner removed successfully",
  "data": {
    "idbc": "123456789"
  }
}
```

### Error Response - Account Not Found (404 Not Found)

```json
{
  "code": "404",
  "message": "Account not found"
}
```

### Error Response - Partner Not Found or Other Error (400 Bad Request)

```json
{
  "code": "400",
  "message": "Error message from service"
}
```

### Usage Examples

**cURL:**
```bash
curl -X DELETE "https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners/123456789" \
  -H "X-API-Key: your-api-key-here"
```

**JavaScript (Axios):**
```javascript
const axios = require('axios');

axios.delete(
  'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners/123456789',
  {
    headers: {
      'X-API-Key': 'your-api-key-here'
    }
  }
)
.then(response => {
  console.log('Partner removed:', response.data.data.idbc);
})
.catch(error => {
  console.error('Error:', error.response.data);
});
```

**Python (Requests):**
```python
import requests

headers = {
    'X-API-Key': 'your-api-key-here'
}

response = requests.delete(
    'https://app.cadimar.net/api/dealer/tiktok/accounts/7234567890123456789/partners/123456789',
    headers=headers
)

if response.status_code == 200:
    print(f"Partner removed: {response.json()['data']['idbc']}")
else:
    print(f"Error: {response.json()['message']}")
```

### Notes

- The TikTok account must belong to the dealer making the API call
- IDBC must exist in the account's partners list
- After removal, the partner will no longer have access to the account

---

## Error Codes and Meanings

### HTTP Status Codes

| Code | Meaning | When It Occurs |
|------|---------|----------------|
| 200 | OK | Request successful |
| 202 | Accepted | Transaction is being processed |
| 400 | Bad Request | Invalid request data |
| 403 | Forbidden | Invalid API Key or no permission |
| 404 | Not Found | Account not found or does not belong to dealer |
| 500 | Internal Server Error | Unexpected server error |

### Response Codes (in body)

| Code | Meaning |
|------|---------|

| "200" | Success |
| "202" | Processing |
| "400" | Input data error |
| "403" | No access permission |
| "404" | Not found |
| "500" | Server error |

### Transaction Status

| Status | Meaning |
|---------|---------|

| SUCCESS | Transaction successful |
| FAILED | Transaction failed |
| PENDING | Awaiting processing |
| ERROR | System error |

---

## Best Practices

### 1. Rate Limiting
- Do not exceed 100 requests/minute
- Implement exponential backoff when encountering 429 errors

### 2. Error Handling
```javascript
try {
  const response = await axios.get(url, { headers });
  // Handle success
} catch (error) {
  if (error.response) {
    // Server returned error response
    console.error('Error:', error.response.data.message);
  } else if (error.request) {
    // Request made but no response
    console.error('No response from server');
  } else {
    // Request setup error
    console.error('Request error:', error.message);
  }
}
```

### 3. API Key Security
- **DO NOT** commit API keys to source code
- Store in environment variables
- Rotate API keys periodically
- Use HTTPS for all requests

### 4. Logging
- Log all API calls with timestamps
- Log response codes for monitoring
- Do not log sensitive data (API keys, account balances)

### 5. Retry Logic
```python
import time
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

session = requests.Session()
retry = Retry(
    total=3,
    backoff_factor=1,
    status_forcelist=[500, 502, 503, 504]
)
adapter = HTTPAdapter(max_retries=retry)
session.mount('https://', adapter)

response = session.get(url, headers=headers)
```

---

## Support Contact

- **Email**: support@cadimar.net
- **Website**: https://app.cadimar.net
- **API Version**: 1.0.1
- **Last Updated**: January 11, 2026

---

## Changelog

### v1.0.1 (2026-01-11)
- Added Auto-Deposit configuration endpoint
- Enhanced auto-deposit modes (unlimited, max times, total amount)
- Updated account list response to include auto-deposit fields

### v1.0.0 (2025-12-29)
- Initial API documentation
- TikTok accounts management endpoints
- Transaction endpoints
- Partner management endpoints
