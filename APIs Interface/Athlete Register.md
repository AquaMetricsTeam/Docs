# Athlete Registration

## Request Body

```json
{
  "fullName": "Ahmed Mohamed",
  "email": "ahmed@example.com",
  "password": "Password123!",
  "phoneNumber": "01012345678",
  "eligibilityDocument": "PDF file",
  "gender": 1,
  "dateOfBirth": "2005-06-15",
  "emergencyContact": "01098765432",
  "profilePicture": "Image file"
}
```

## Response Body

```json
{
  "athleteId": "7f8b7c2d-1234-4567-8901-abcdef123456",
  "fullName": "Ahmed Mohamed",
  "email": "ahmed@example.com",
  "registrationStatus": 1
}
```
