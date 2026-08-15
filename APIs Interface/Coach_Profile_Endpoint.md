# Coach Profile

## Endpoint

`GET /api/coach-profile`

### Authorization

Requires authentication with the `Coach` role.

### Request Body

No request body is required.

The coach is identified from the authenticated user context.

### Response Body

```json
{
  "data": {
    "id": "00000000-0000-0000-0000-000000000000",
    "fullName": "Ahmed Mohamed",
    "email": "coach@example.com",
    "age": 30,
    "gender": 1,
    "profilePictureUrl": "https://example.com/profile.jpg",
    "phoneNumber": "01012345678",
    "assignedAthletes": [
      {
        "athleteId": "00000000-0000-0000-0000-000000000000",
        "fullName": "Omar Ali",
        "email": "omar@example.com",
        "profilePictureUrl": "https://example.com/omar.jpg"
      }
    ]
  },
  "message": null,
  "isSuccess": true,
  "errors": []
}
```
