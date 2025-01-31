# API Documentation: Cross-Border Payment Endpoint

## Overview
The `POST /api/v1/payments` endpoint is used to initiate a cross-border payment between a sender and a recipient. This API allows you to transfer funds internationally by specifying the sender's and recipient's details, the amount, currency, and a unique reference for the transaction.

---

## Request

### HTTP Method
- **POST** `/api/v1/payments`

### Headers
| Header Name       | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| `Authorization`   | Bearer token for authentication. Format: `Bearer <your_api_key>`.          |
| `Content-Type`    | Specifies the format of the request body. Must be `application/json`.       |

### Body Parameters
The request body must be in JSON format and include the following fields:

| Field            | Type     | Description                                                                 |
|------------------|----------|-----------------------------------------------------------------------------|
| `amount`         | `number` | The amount to be transferred. Must be a positive number.                   |
| `currency`       | `string` | The currency code for the transaction (e.g., `USD`).                       |
| `sender`         | `object` | Details of the sender.                                                     |
| `sender.name`    | `string` | Full name of the sender.                                                   |
| `sender.email`   | `string` | Email address of the sender.                                               |
| `recipient`      | `object` | Details of the recipient.                                                  |
| `recipient.name` | `string` | Full name of the recipient.                                                |
| `recipient.accountNumber` | `string` | Recipient's bank account number.                                   |
| `recipient.bankCode`      | `string` | Bank code of the recipient's bank.                                  |
| `recipient.country`       | `string` | Country code of the recipient (e.g., `USA`).                        |
| `reference`      | `string` | A unique reference for the transaction (e.g., invoice number).             |

#### Example Request Body
```json
{
  "amount": 100.00,
  "currency": "USD",
  "sender": {
    "name": "John Doe",
    "email": "john.doe@x.com"
  },
  "recipient": {
    "name": "Jane Smith",
    "accountNumber": "0987654321",
    "bankCode": "XYZ456",
    "country": "USA"
  },
  "reference": "INV-12345"
}
```

## Response
- Success Response (HTTP 201)

A successful request will return a `201 Created` status code with the following response body:

| Field                | Type     | Description                                                         |
|----------------------|----------|---------------------------------------------------------------------|
| `transactionId`      | `string` | Unique identifier for the transaction.                              |
| `status`             | `string` | Status of the transaction (e.g., `SUCCESS`).                        |
| `createdAt`          | `string` | Timestamp of when the transaction was created (ISO 8601 format).    |
| `amount`             | `number` | The amount transferred. Must be a positive number.                  |
| `currency`           | `string` | The currency code for the transaction.                              |
| `recipient`          | `object` | Details of the recipient.                                           |
| `recipient.name`     | `string` | Full name of the recipient.                                         |
| `recipient.country`  | `string` | Country code of the recipient.                                      |

### Example Success Response
```json
{
  "transactionId": "TXN789456123",
  "status": "SUCCESS",
  "createdAt": "2025-01-12T10:15:30Z",
  "amount": 100.00,
  "currency": "USD",
  "recipient": {
    "name": "Jane Smith",
    "country": "USA"
  }
}
```

## Error Response (HTTP 400)
An error will return a `400 Bad Request` status code with the following response body: 
| Field            | Type     | Description                                                                 |
|------------------|----------|-----------------------------------------------------------------------------|
| `error`  | `string` | Error code indicating the type of error (e.g., `INVALID_REQUEST`).                           |
| `message`  | `string` | Human-readable message describing the error.                           |

### Example Error Response
```json
{
 "error": "INVALID_REQUEST",
  "message": "Invalid bank code for the recipient."
}
```

## Error Handling
Below are common error responses you may encounter:
| HTTP Status Code            | Error Code     | Description                                                                 |
|------------------|----------|-----------------------------------------------------------------------------|
| `400`  | `INVALID_REQUEST` | The request is malformedor contains invalid data (e.g., invalid bank code).                           |
| `401`  | `UNAUTHORIZED` | The API key is missing or invalid.                           |
| `403`  | `FORBIDDEN` | The user does not have permission to perform this action.                           |
| `500`  | `INTERNAL_ERROR` | An unexpected error occured on the server.                           |

## Examples
### Valid Request and Success Response
**Request:**
```bash
POST "/api/v1/payments"
Headers:
    "Authorization: Bearer <your_api_key>" 
    "Content-Type: application/json" 

Body:
{
 "amount": 100.00,
  "currency": "USD",
  "sender": {
    "name": "John Doe",
    "email": "john.doe@x.com"
  },
  "recipient": {
    "name": "Jane Smith",
    "accountNumber": "0987654321",
    "bankCode": "XYZ456",
    "country": "USA"
  },
  "reference": "INV-12345"
}
```
**Response:**
```bash
HTTP/1.1 201 Created
{
  "transactionId": "TXN789456123",
  "status": "SUCCESS",
  "createdAt": "2025-01-12T10:15:30Z",
  "amount": 100.00,
  "currency": "USD",
  "recipient": {
    "name": "Jane Smith",
    "country": "USA"
  }
}
```

### Error Response
**Request:**
```bash
POST "/api/v1/payments"
Headers:
    "Authorization: Bearer <your_api_key>" 
    "Content-Type: application/json" 

Body:
{
 "amount": 100.00,
  "currency": "USD",
  "sender": {
    "name": "John Doe",
    "email": "john.doe@x.com"
  },
  "recipient": {
    "name": "Jane Smith",
    "accountNumber": "0987654321",
    "bankCode": "INVALID_CODE",
    "country": "USA"
  },
  "reference": "INV-12345"
}
```
**Response:**
```bash
HTTP/1.1 400 Bad Request
{
  "error": "INVALID_REQUEST",
  "message": "Invalid bank code for the recipient."
}
```
