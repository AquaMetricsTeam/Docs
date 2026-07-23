# Current User API

## Endpoint

`GET http://aquametrics.runasp.net/api/Auth/me`

---

## Description

Returns the currently authenticated user's information.

This endpoint is typically called when:

- The application starts.
- The user refreshes the browser.
- The mobile application is reopened.

The endpoint uses the Access Token to identify the user.

---

## Authentication

Requires a valid Access Token.

Authorization Header:

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

---

## Request

```http
GET /api/Auth/me
```

No request body.

---

## Success Response (200 OK)

```json
{
  "success": true,
  "message": "User information retrieved successfully.",
  "data": {
    "userId": "ec20c6a9-4554-4060-736d-08dee7622e59",
    "fullName": "System Administrator",
    "email": "admin@aqua.com",
    "roles": ["Admin"]
  }
}
```

---

## Error Responses

### Unauthorized

```json
{
  "success": false,
  "message": "Unauthorized."
}
```

---

### Invalid Token

```json
{
  "success": false,
  "message": "Invalid access token."
}
```
