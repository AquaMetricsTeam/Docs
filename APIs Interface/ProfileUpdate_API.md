# Profile API Documentation

All profile endpoints require authentication.

Base Route:

```text
/api/profile
```

authentication
Authorization: Bearer <access_token>

## 1. Update Profile

### Endpoint

PUT /api/profile

#### Request

```json
{
  "phoneNumber": "+201012345678",
  "medicalNotes": "No known injuries",
  "emergencyContact": "+201098765432",
  "dateOfBirth": "2001-05-15",
  "gender": 0
}
```

| Property           | Type      | Required | Description                                |
| ------------------ | --------- | -------: | ------------------------------------------ |
| `phoneNumber`      | `string`  |      Yes | User's phone number                        |
| `medicalNotes`     | `string`  |       No | Medical notes associated with the user     |
| `emergencyContact` | `string`  |       No | Emergency contact information              |
| `dateOfBirth`      | `date`    |       No | User's date of birth. Format: `YYYY-MM-DD` |
| `gender`           | `integer` |       No | User's gender enum value                   |

#### DTO

public class UpdateProfileRequest
{
public string PhoneNumber { get; set; } = string.Empty;

    public string? MedicalNotes { get; set; }

    public string? EmergencyContact { get; set; }

    public DateOnly? DateOfBirth { get; set; }

    public Gender? Gender { get; set; }

}

## 2. Change Password

### Endpoint

PUT /api/profile/change-password

#### Request

```json
{
  "currentPassword": "OldPassword123!",
  "newPassword": "NewPassword123!",
  "confirmPassword": "NewPassword123!"
}
```

| Property          | Type     | Required | Description                 |
| ----------------- | -------- | -------: | --------------------------- |
| `currentPassword` | `string` |      Yes | The user's current password |
| `newPassword`     | `string` |      Yes | The new password            |
| `confirmPassword` | `string` |      Yes | Must match `newPassword`    |

## 3.Upload Profile Picture

### Endpoint

POST /api/profile/profile-picture

### Content Type

multipart/form-data

#### Request

POST /api/profile/profile-picture
Authorization: Bearer <access_token>
Content-Type: multipart/form-data

| Method | Endpoint                       | Description                    | Body                |
| ------ | ------------------------------ | ------------------------------ | ------------------- |
| `PUT`  | `/api/profile`                 | Update current user's profile  | JSON                |
| `PUT`  | `/api/profile/change-password` | Change current user's password | JSON                |
| `POST` | `/api/profile/profile-picture` | Upload profile picture         | Multipart Form Data |
