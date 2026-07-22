# Aqua Metrics API Documentation

# Authentication API

Base URL

```
https://localhost:xxxx/api/Auth
```

---

# Overview

The Aqua Metrics platform uses **JWT Authentication** with **Refresh Token Rotation**.

After a successful login:

- An **Access Token (JWT)** is returned.
- A **Refresh Token** is returned.
- The Access Token is used to authorize API requests.
- The Refresh Token is used to obtain a new Access Token when the current one expires.

---

# User Roles

The system currently supports the following roles:

| Role                | Description          |
| ------------------- | -------------------- |
| Admin               | System Administrator |
| SwimmingCoach       | Swimming Coach       |
| FitnessCoach        | Fitness Coach        |
| NutritionSpecialist | Nutrition Specialist |
| Athlete             | Athlete              |

> **Important**
>
> Role names are case-sensitive.
> Always use the exact names above.

---

# Default Administrator Account

Development Environment Only

Email

```
admin@aqua.com
```

Password

```
Admin@123
```

---

# Login

Authenticates a user and returns an Access Token and Refresh Token.

### Endpoint

```
POST /login
```

---

## Request

```json
{
  "email": "admin@aqua.com",
  "password": "Admin@123"
}
```

---

## Successful Response (200 OK)

```json
{
  "accessToken": "eyJhbGc...",
  "refreshToken": "4A2D87A4E53D4F5A...",
  "expiration": "2026-07-22T16:45:00Z",
  "userId": "ec20c6a9-4554-4060-736d-08dee7622e59",
  "fullName": "System Administrator",
  "email": "admin@aqua.com",
  "roles": ["Admin"]
}
```

---

## Possible Responses

| Status Code | Description               |
| ----------- | ------------------------- |
| 200         | Login successful          |
| 401         | Invalid email or password |
| 403         | Account is inactive       |
| 400         | Validation failed         |

---

# Using the Access Token

Every protected endpoint requires the Access Token.

Add the following header:

```
Authorization: Bearer {accessToken}
```

Example

```
Authorization: Bearer eyJhbGc...
```

---

# Refresh Token

Obtains a new Access Token without requiring the user to log in again.

The previous Refresh Token becomes invalid immediately after use.

This implementation follows **Refresh Token Rotation**.

---

## Endpoint

```
POST /refresh
```

---

## Request

```json
{
  "refreshToken": "4A2D87A4E53D4F5A..."
}
```

---

## Successful Response (200 OK)

```json
{
  "accessToken": "eyJhbGc...",
  "refreshToken": "6F8A98D7EAA2F44...",
  "expiration": "2026-07-22T17:50:00Z",
  "userId": "ec20c6a9-4554-4060-736d-08dee7622e59",
  "fullName": "System Administrator",
  "email": "admin@aqua.com",
  "roles": ["Admin"]
}
```

---

## Important

After a successful refresh:

- The old Refresh Token is revoked.
- The old Refresh Token cannot be used again.
- Always replace the stored Refresh Token with the newly returned one.

---

## Possible Responses

| Status Code | Description                  |
| ----------- | ---------------------------- |
| 200         | Token refreshed successfully |
| 401         | Invalid refresh token        |
| 401         | Refresh token expired        |
| 401         | Refresh token revoked        |
| 403         | User account is inactive     |

---

# Logout

Logs the user out by revoking the current Refresh Token.

---

## Endpoint

```
POST /logout
```

---

## Request

```json
{
  "refreshToken": "6F8A98D7EAA2F44..."
}
```

---

## Successful Response

```
204 No Content
```

---

## Notes

- Logout only revokes the provided Refresh Token.
- The associated Access Token remains valid until it expires.
- After logout, the Refresh Token cannot be reused.

---

# Authentication Flow

```
Login
    │
    ▼
Receive Access Token + Refresh Token
    │
    ▼
Use Access Token in Authorization Header
    │
    ▼
Access Token Expires
    │
    ▼
Call /refresh
    │
    ▼
Receive New Access Token + New Refresh Token
    │
    ▼
Replace Stored Tokens
```

---

# Security Notes

- Access Tokens are JWTs.
- Refresh Tokens are stored securely on the server.
- Refresh Tokens are single-use.
- Refresh Token Rotation is implemented.
- Only active accounts may authenticate.
- Role-Based Access Control (RBAC) is enforced using JWT Role Claims.

---

# Frontend & Mobile Responsibilities

The client application must:

- Store the Access Token securely.
- Store the latest Refresh Token securely.
- Include the Access Token in the Authorization header.
- Automatically call `/refresh` when the Access Token expires.
- Replace both stored tokens after every successful refresh.
- Call `/logout` before signing the user out.

---

# Authentication Sequence

```
User
 │
 │ Login
 ▼
API
 │
 ├── Validate Credentials
 ├── Generate JWT
 ├── Generate Refresh Token
 ├── Save Refresh Token
 ▼
Client
 │
 │ Use Access Token
 ▼
Protected APIs
 │
 │ Access Token Expired
 ▼
POST /refresh
 │
 ├── Validate Refresh Token
 ├── Revoke Old Refresh Token
 ├── Generate New JWT
 ├── Generate New Refresh Token
 ▼
Client
```
