# Evernorth Backend API Documentation

## Authentication APIs

Authentication in the Evernorth backend is a two-step process for both user registration and login. This ensures security by verifying user identity before storing details or granting access.

---

## Sign-Up Process (2-Step)

The sign-up process consists of two steps:

1. **User Registration**: The user submits their details to initiate the registration process.
2. **Email Verification**: The user must verify their email using an OTP. Only after successful verification are the user details stored in the database, and a JWT token is issued.

### 1. User Registration

This endpoint is used to initiate the user registration process. Upon calling this API, an OTP is sent to the user's email for verification.

**Endpoint:**

```
POST /api/auth/register
```

**Request Parameters:**

- `fullName` (string, required): The full name of the user.
- `email` (string, required): The email address of the user.
- `contact` (string, required): The contact number of the user.
- `dob` (string, required): The date of birth of the user in `YYYY-MM-DD` format.

**Example Request:**

```http
POST /api/v1/auth/register HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "fullName": "John Doe",
    "email": "kumbhamajaygoud2004@gmail.com",
    "contact": "1234567890",
    "dob": "1990-01-01"
}
```

**Response:**

```http
200 OK
```

```
Registration initiated. Please verify your email with the OTP sent.
```

**Note:** Upon successful email verification, a JWT token is issued.

---

### 2. Email Verification

This endpoint verifies the OTP sent to the user's email. Only upon successful verification are the user details stored in the database, and a JWT token is issued for authentication.

**Endpoint:**

```
POST /api/auth/verify-email
```

**Request Parameters:**

- `email` (string, required): The email address of the user.
- `otp` (string, required): The OTP sent to the user's email.

**Example Request:**

```http
POST /api/v1/auth/verify-email HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "email": "kumbhamajaygoud2004@gmail.com",
    "otp": "813155"
}
```

**Response:**

```http
200 OK
```

```json
{
    "token": "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKOTAwMSIsImlhdCI6MTczODQ3MzcwNiwiZXhwIjoxNzM4NTYwMTA2fQ.OLYEJtT8lXLiqAzxmNfBjQ4m7Tr3P289LcusvLjOt-I"
}
```

**Note:** The issued token must be used for authenticated requests.

---

## Login Process (2-Step)

The login process consists of two steps:

1. **Send OTP for Login**: The user requests an OTP to be sent to their registered email.
2. **Verify OTP and Retrieve Token**: The user enters the OTP to verify their identity and receive a JWT token for authentication.

### 3. Send OTP for Login

This endpoint is used to send an OTP to the registered email for login authentication.

**Endpoint:**

```
POST /api/auth/login/send-otp
```

**Request Parameters:**

- `email` (string, required): The registered email address of the user.

**Example Request:**

```http
POST /api/v1/auth/login/send-otp HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "email": "kumbhamajaygoud2004@gmail.com"
}
```

**Response:**

```http
200 OK
```

```
OTP sent successfully
```

---

### 4. Verify OTP for Login

This endpoint verifies the OTP entered by the user and returns a JWT token that is valid for authentication.

**Endpoint:**

```
POST /api/auth/login/verify-otp
```

**Request Parameters:**

- `email` (string, required): The registered email address of the user.
- `otp` (string, required): The OTP received via email.

**Example Request:**

```http
POST /api/v1/auth/login/verify-otp HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "email": "kumbhamajaygoud2004@gmail.com",
    "otp": "848734"
}
```

**Response:**

```http
200 OK
```

```json
{
    "token": "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKOTAwMSIsImlhdCI6MTczODQyMDY3OCwiZXhwIjoxNzM4NTA3MDc4fQ.YwEjcQuBGJpl-DshR1-ffM5iiVcqu6Il_cfchuiT_Ww"
}
```

**Note:** The token returned is a JWT token and is valid for exactly **24 hours (1 day)**. This token must be included in the Authorization header for accessing protected resources.

---

## Payment APIs

The Evernorth Payment APIs allow users to manage their payment methods, including retrieving, adding, updating, and deleting stored payment details. It supports multiple payment types:

- **Credit Card**
- **Debit Card**
- **UPI**
- **Bank Transfer**

This API ensures secure transactions by masking sensitive information such as card numbers and bank account details.

### Authentication
All requests require authentication via a **Bearer Token** in the request header. Unauthorized requests will be denied.

### Authorization Header Example
```http
Authorization: Bearer <token>
```

---

## Endpoints

### 1. Retrieve All Payment Methods

#### Endpoint
```http
GET /api/users/payments
```

#### Description
Retrieves a list of all stored payment methods for the authenticated user.

#### Request Example
```http
GET /api/users/payments HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
```

#### Response Example
```json
[
    {
        "paymentType": "creditcard",
        "maskedCardNumber": "**3456",
        "upiId": null,
        "nameOnCard": "John Doe",
        "expiryDate": "2025-12-31",
        "cardType": "VISA",
        "accountHolderName": null,
        "maskedBankAccountNumber": null,
        "ifscCode": null
    },
    {
        "paymentType": "debitcard",
        "maskedCardNumber": "**1234",
        "upiId": null,
        "nameOnCard": "John Doe",
        "expiryDate": "2026-08-31",
        "cardType": "MasterCard",
        "accountHolderName": null,
        "maskedBankAccountNumber": null,
        "ifscCode": null
    },
    {
        "paymentType": "upi",
        "maskedCardNumber": null,
        "upiId": "john.doe@upi",
        "nameOnCard": null,
        "expiryDate": null,
        "cardType": null,
        "accountHolderName": null,
        "maskedBankAccountNumber": null,
        "ifscCode": null
    },
    {
        "paymentType": "banktransfer",
        "maskedCardNumber": null,
        "upiId": null,
        "nameOnCard": null,
        "expiryDate": null,
        "cardType": null,
        "accountHolderName": "John Doe",
        "maskedBankAccountNumber": "**7890",
        "ifscCode": "ABCD0123456"
    }
]
```

#### Response Codes
- **200 OK** – Payment methods retrieved successfully.
- **401 Unauthorized** – Invalid or missing token.

---
### 2. Add a New Payment Method

#### Endpoint

```http
POST /api/users/payments
```

#### Description

Adds a new payment method for the user. The request body should only contain relevant fields for the specified payment type. In the response, all fields will be present, but non-applicable ones will be set to `null`.

#### Example Requests & Responses

#### I.Adding a Credit Card

**Request:**
```http
POST /api/users/payments HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```
{
    "paymentType": "creditcard",
    "maskedCardNumber": "**3456",
    "nameOnCard": "John Doe",
    "expiryDate": "2025-12-31",
    "cardType": "VISA"
}
```

**Response:**
```json
{
    "paymentType": "creditcard",
    "maskedCardNumber": "**3456",
    "upiId": null,
    "nameOnCard": "John Doe",
    "expiryDate": "2025-12-31",
    "cardType": "VISA",
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

#### II.Adding a Debit Card

**Request:**
```http
POST /api/users/payments HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "paymentType": "debitcard",
    "maskedCardNumber": "**1234",
    "nameOnCard": "John Doe",
    "expiryDate": "2026-08-31",
    "cardType": "MasterCard"
}
```

**Response:**
```json
{
    "paymentType": "debitcard",
    "maskedCardNumber": "**1234",
    "upiId": null,
    "nameOnCard": "John Doe",
    "expiryDate": "2026-08-31",
    "cardType": "MasterCard",
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

#### III.Adding a UPI Payment Method

**Request:**
```http
POST /api/users/payments HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "paymentType": "upi",
    "upiId": "john.doe@upi"
}
```

**Response:**
```json
{
    "paymentType": "upi",
    "maskedCardNumber": null,
    "upiId": "john.doe@upi",
    "nameOnCard": null,
    "expiryDate": null,
    "cardType": null,
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

#### IV.Adding a Bank Transfer

**Request:**
```http
POST /api/users/payments HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "paymentType": "banktransfer",
    "accountHolderName": "John Doe",
    "maskedBankAccountNumber": "**7890",
    "ifscCode": "ABCD0123456"
}
```

**Response:**
```json
{
    "paymentType": "banktransfer",
    "maskedCardNumber": null,
    "upiId": null,
    "nameOnCard": null,
    "expiryDate": null,
    "cardType": null,
    "accountHolderName": "John Doe",
    "maskedBankAccountNumber": "**7890",
    "ifscCode": "ABCD0123456"
}
```

#### Response Codes

- **201 Created** – Payment method added successfully.
- **400 Bad Request** – Invalid input format.
- **401 Unauthorized** – Invalid or missing token.

### 3. Update a Payment Method

#### Endpoint

```http
PUT /api/users/payments/{paymentType}
```

#### Description

Updates an existing payment method. Only applicable fields for the specific payment type should be included.
The `paymentType` itself cannot be updated.

---

#### Example Requests & Responses for Each Payment Type

#### I. Updating a Credit Card

**Request:**
```
PUT /api/users/payments/creditcard HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "expiryDate": "2026-07-31",
    "cardType": "MasterCard"
}
```

**Response:**
```json
{
    "paymentType": "creditcard",
    "maskedCardNumber": "**3456",
    "upiId": null,
    "nameOnCard": "John Doe",
    "expiryDate": "2026-07-31",
    "cardType": "MasterCard",
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

---

#### II. Updating a Debit Card

**Request:**
```
PUT /api/users/payments/debitcard HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "expiryDate": "2026-10-15",
    "cardType": "MasterCard"
}
```

**Response:**
```json
{
    "paymentType": "debitcard",
    "maskedCardNumber": "**1234",
    "upiId": null,
    "nameOnCard": "John Doe",
    "expiryDate": "2026-10-15",
    "cardType": "MasterCard",
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

---

#### III. Updating a UPI Payment Method

**Request:**
```
PUT /api/users/payments/upi HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "upiId": "john.new@upi"
}
```

**Response:**
```json
{
    "paymentType": "upi",
    "maskedCardNumber": null,
    "upiId": "john.new@upi",
    "nameOnCard": null,
    "expiryDate": null,
    "cardType": null,
    "accountHolderName": null,
    "maskedBankAccountNumber": null,
    "ifscCode": null
}
```

---

#### IV. Updating a Bank Transfer

**Request:**
```
PUT /api/users/payments/banktransfer HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
    "accountHolderName": "John D.",
    "ifscCode": "WXYZ9876543"
}
```

**Response:**
```json
{
    "paymentType": "banktransfer",
    "maskedCardNumber": null,
    "upiId": null,
    "nameOnCard": null,
    "expiryDate": null,
    "cardType": null,
    "accountHolderName": "John D.",
    "maskedBankAccountNumber": "**7890",
    "ifscCode": "WXYZ9876543"
}
```

---

#### Response Codes for PUT Requests

- **200 OK** – Payment method updated successfully.
- **400 Bad Request** – Invalid request format.
- **401 Unauthorized** – Invalid or missing token.
- **404 Not Found** – Payment method not found.

---

### 4.Delete a Payment Method

#### Endpoint

```http
DELETE  /api/users/payments/{paymentType}
```

#### Description

Deletes a saved payment method based on its type.

#### Request Example

```
DELETE /api/users/payments/upi HTTP/1.1
Host: localhost:8080
Authorization: Bearer <token>
```

#### Response Codes

- **200 OK** – Payment method deleted successfully.
- **401 Unauthorized** – Invalid or missing token.
- **404 Not Found** – Payment method not found.

---

#### Notes

- Each user can store one payment method per type.
- `POST` and `PUT` requests must only include applicable fields; missing fields will be `null` in the response.
- Masked details (card numbers, bank accounts) ensure security.
- Bearer token authentication is mandatory for all requests.

Here is the "Get User Addresses" section in the same format as your existing documentation:

---

## Address APIs

The Address API allows users to store, retrieve, update, and remove addresses associated with their account. It provides a structured way to handle user address data, ensuring consistency and security across various applications such as delivery services, billing, and user profiles.

Users can add new addresses, fetch their saved addresses, modify existing ones, or delete them when no longer needed. Each operation requires authentication via a Bearer token, and the API returns appropriate HTTP status codes to indicate the success or failure of the request.


### Authentication
All requests require authentication via a **Bearer Token** in the request header. Unauthorized requests will be denied.

### Authorization Header Example
```http
Authorization: Bearer <token>
```

### Common Address Fields:
- **addressLabel** (string) - A label for the address (e.g., "Home", "Work"). *(Required)*
- **addressLine1** (string) - Primary address line (e.g., street name, house number). *(Required)*
- **addressLine2** (string) - Additional address details (e.g., apartment number, landmark). *(Optional)*
- **city** (string) - Name of the city. *(Required)*
- **state** (string) - Name of the state. *(Required)*
- **zipCode** (string) - Postal code of the address. *(Required)*
- **landmark** (string) - Nearby landmark for easier identification. *(Optional)*

---

### Endpoints

#### 1. Get All Addresses
**Request:**
```
GET /api/users/addresses
Host: localhost:8080
Authorization: Bearer <token>
```

**Response:**
```json
[
    {
        "addressLabel": "Home",
        "addressLine1": "123 Main St",
        "addressLine2": "Apt 4B",
        "city": "New York",
        "state": "NY",
        "zipCode": "10001",
        "landmark": "Near Central Park"
    }
]
```

---

#### 2. Add a New Address
**Request:**
```
POST /api/users/addresses
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Body:**
```json
{
    "addressLabel": "Home",
    "addressLine1": "123 Main St",
    "addressLine2": "Apt 4B",
    "city": "New York",
    "state": "NY",
    "zipCode": "10001",
    "landmark": "Near Central Park"
}
```

**Response:**
```json
{
    "addressLabel": "Home",
    "addressLine1": "123 Main St",
    "addressLine2": "Apt 4B",
    "city": "New York",
    "state": "NY",
    "zipCode": "10001",
    "landmark": "Near Central Park"
}
```

---

#### 3. Update an Existing Address
**Request:**
```
PUT /api/users/addresses/{addressLabel}
Host: localhost:8080
Authorization: Bearer <token>
Content-Type: application/json
```

**Body:**
```json
{
    "addressLabel": "Home",
    "addressLine1": "123 Main St",
    "addressLine2": "Apt 4B",
    "city": "Hyderabad",
    "zipCode": "10001",
    "landmark": "Near Central Vista"
}
```

**Response:**
```json
{
    "addressLabel": "Home",
    "addressLine1": "123 Main St",
    "addressLine2": "Apt 4B",
    "city": "Hyderabad",
    "state": "NY",
    "zipCode": "10001",
    "landmark": "Near Central Vista"
}
```

---

#### 4. Delete an Address
**Request:**
```
DELETE /api/users/addresses/{addressLabel}
Host: localhost:8080
Authorization: Bearer <token>
```

**Response:**
```
200 OK (No response body)
```

---

#### Notes:
- Ensure all required fields are provided in the request body.
- **addressLine2** and **landmark** are optional fields and can be omitted.
- The API uses **addressLabel** as a unique identifier for updating and deleting addresses.



