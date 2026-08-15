# Athlete Registration – Admin Review APIs

## 1. Get Pending Athlete Accounts

### Endpoint

```http
GET /api/users/athletes/pending
```

### Request

No request body.

### Response

```json
{
  "succeeded": true,
  "message": "Pending athletes retrieved successfully.",
  "data": [
    {
      "athleteId": "00000000-0000-0000-0000-000000000000",
      "fullName": "Ahmed Ali",
      "email": "ahmed@example.com",
      "registrationStatus": "Pending",
      "profilePictureUrl": "https://...",
      "eligibilityDocumentUrl": "https://..."
    }
  ]
}
```

---

## 2. Approve Athlete Registration

### Endpoint

```http
POST /api/users/athletes/{athleteId}/approve
```

### Request

No request body.

### Response

```json
{
  "succeeded": true,
  "message": "Athlete registration approved successfully.",
  "data": {
    "athleteId": "00000000-0000-0000-0000-000000000000",
    "fullName": "Ahmed Ali",
    "email": "ahmed@example.com",
    "registrationStatus": "Active"
  }
}
```

---

## 3. Reject Athlete Registration

### Endpoint

```http
POST /api/users/athletes/{athleteId}/reject
```

### Request

No request body.

### Response

```json
{
  "succeeded": true,
  "message": "Athlete registration rejected successfully.",
  "data": {
    "athleteId": "00000000-0000-0000-0000-000000000000",
    "fullName": "Ahmed Ali",
    "email": "ahmed@example.com",
    "registrationStatus": "Suspended"
  }
}
```

> Note: If you want a dedicated `Rejected` status later, add it to `RegistrationStatus`. With the current enum, `Suspended` is the existing status that can represent a rejected registration.
