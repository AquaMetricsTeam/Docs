# AquaMetrics API Documentation

> **Base URL:** `http://aquametrics.runasp.net/api`  
> **Standard Response Wrapper:** Every successful response is wrapped in `ApiResponse<T>`.

---

## Table of Contents

1. [Common Types](#1-common-types)
2. [Auth](#2-auth)
3. [Users (Admin Only)](#3-users-admin-only)
4. [Profile](#4-profile)
5. [Notifications](#5-notifications)
6. [Coach Assignments](#6-coach-assignments-admin-only)
7. [Coach Notes](#7-coach-notes-coach-only)
8. [Groups](#8-groups-admin--coach)
9. [Exercises](#9-exercises-coach-only)
10. [Training Plans](#10-training-plans-coach-only)
11. [Test](#12-test)

---

## 1. Common Types

### ApiResponse<T>

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {
    /* T */
  },
  "statusCode": 200
}
```

### PagedResponse<T>

```json
{
  "items": [
    /* T[] */
  ],
  "pageNumber": 1,
  "pageSize": 10,
  "totalCount": 100,
  "totalPages": 10,
  "hasPrevious": false,
  "hasNext": true
}
```

### PaginationRequest (Query Params)

| Parameter  | Type | Default | Description        |
| ---------- | ---- | ------- | ------------------ |
| PageNumber | int  | 1       | Page number        |
| PageSize   | int  | 10      | Max 50 per request |

### Enums

#### Gender

- `1` = Male
- `2` = Female
- `3` = Unspecified

#### ExerciseIntensity

- `1` = Low
- `2` = Medium
- `3` = High

#### AttendanceStatus

- `1` = Present
- `2` = Absent
- `3` = Late
- `4` = Excused

#### NotificationType

- `1` = AccountCreated
- `2` = TrainingAssigned
- `3` = NutritionAssigned
- `4` = AssessmentRecorded
- `5` = AttendanceRecorded
- `6` = AIRecommendationReady
- `7` = SystemAnnouncement
- `8` = GroupAssigned
- `9` = TrainingSessionAssigned

---

## 2. Auth

Base: `api/auth`

---

### 2.1 Login

| Property   | Value             |
| ---------- | ----------------- |
| **Method** | `POST`            |
| **URL**    | `/api/auth/login` |
| **Auth**   | None              |

**Request Body:**

```json
{
  "email": "coach@aquametrics.com",
  "password": "SecurePass123!"
}
```

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2g...",
    "expiration": "2025-07-28T12:00:00Z",
    "userId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "fullName": "Coach Ahmed",
    "email": "coach@aquametrics.com",
    "roles": ["Coach"]
  },
  "statusCode": 200
}
```

---

### 2.2 Refresh Token

| Property   | Value               |
| ---------- | ------------------- |
| **Method** | `POST`              |
| **URL**    | `/api/auth/refresh` |
| **Auth**   | None                |

**Request Body:**

```json
{
  "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2g..."
}
```

**Response (200 OK):**

Same shape as Login response.

---

### 2.3 Get Current User (Me)

| Property   | Value          |
| ---------- | -------------- |
| **Method** | `GET`          |
| **URL**    | `/api/auth/me` |
| **Auth**   | Bearer Token   |

**Response (200 OK):**

```json
{
  "success": true,
  "message": "User information retrieved successfully.",
  "data": {
    "userId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "fullName": "Coach Ahmed",
    "email": "coach@aquametrics.com",
    "roles": ["Coach"]
  },
  "statusCode": 200
}
```

---

### 2.4 Logout

| Property   | Value              |
| ---------- | ------------------ |
| **Method** | `POST`             |
| **URL**    | `/api/auth/logout` |
| **Auth**   | None (or Bearer)   |

**Request Body:**

```json
{
  "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2g..."
}
```

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Logged out successfully.",
  "data": null,
  "statusCode": 200
}
```

---

## 3. Users (Admin Only)

Base: `api/users`

All endpoints require `Admin` role.

---

### 3.1 Create User

| Property   | Value                |
| ---------- | -------------------- |
| **Method** | `POST`               |
| **URL**    | `/api/users/Create`  |
| **Auth**   | Bearer Token (Admin) |

**Request Body:**

```json
{
  "fullName": "John Doe",
  "email": "john.doe@aquametrics.com",
  "password": "TempPass123!",
  "role": "Athlete"
}
```

> **Note:** `role` can be `Admin`, `Coach`, or `Athlete`.

**Response (200 OK):**

Wrapped in `ApiResponse<UserResponse>`:

```json
{
  "success": true,
  "message": "User created successfully.",
  "data": {
    "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
    "fullName": "John Doe",
    "email": "john.doe@aquametrics.com",
    "role": "Athlete",
    "isActive": true,
    "createdAt": "2025-07-28T07:30:00Z"
  },
  "statusCode": 200
}
```

---

### 3.2 Get Users (Paged)

| Property   | Value                |
| ---------- | -------------------- |
| **Method** | `GET`                |
| **URL**    | `/api/users/users`   |
| **Auth**   | Bearer Token (Admin) |

**Query Parameters:**

| Parameter  | Type   | Required | Description             |
| ---------- | ------ | -------- | ----------------------- |
| PageNumber | int    | No       | Default: 1              |
| PageSize   | int    | No       | Default: 10, Max: 50    |
| Search     | string | No       | Search by name/email    |
| Role       | string | No       | Filter by role          |
| IsActive   | bool   | No       | Filter by active status |
| SortBy     | string | No       | Sort field              |
| Descending | bool   | No       | Sort direction          |

**Response (200 OK):**

Wrapped in `ApiResponse<PagedResponse<UserResponse>>`:

```json
{
  "success": true,
  "message": "Users retrieved successfully.",
  "data": {
    "items": [
      {
        "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
        "fullName": "John Doe",
        "email": "john.doe@aquametrics.com",
        "role": "Athlete",
        "isActive": true,
        "createdAt": "2025-07-28T07:30:00Z"
      }
    ],
    "pageNumber": 1,
    "pageSize": 10,
    "totalCount": 1,
    "totalPages": 1,
    "hasPrevious": false,
    "hasNext": false
  },
  "statusCode": 200
}
```

---

### 3.3 Update User Status

| Property   | Value                         |
| ---------- | ----------------------------- |
| **Method** | `PATCH`                       |
| **URL**    | `/api/users/{id:guid}/status` |
| **Auth**   | Bearer Token (Admin)          |

**Request Body:**

```json
{
  "isActive": false
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<UserResponse>`.

---

## 4. Profile

Base: `api/profile`

All endpoints require authentication.

---

### 4.1 Get Profile

| Property   | Value          |
| ---------- | -------------- |
| **Method** | `GET`          |
| **URL**    | `/api/profile` |
| **Auth**   | Bearer Token   |

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Profile retrieved successfully.",
  "data": {
    "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "fullName": "Coach Ahmed",
    "email": "coach@aquametrics.com",
    "phoneNumber": "+20 123 456 7890",
    "role": "Coach",
    "profilePictureUrl": "https://cdn.aquametrics.com/profiles/abc.jpg",
    "medicalNotes": null,
    "emergencyContact": null
  },
  "statusCode": 200
}
```

---

### 4.2 Update Profile

| Property   | Value          |
| ---------- | -------------- |
| **Method** | `PUT`          |
| **URL**    | `/api/profile` |
| **Auth**   | Bearer Token   |

**Request Body:**

```json
{
  "phoneNumber": "+20 123 456 7890",
  "medicalNotes": "Asthma inhaler required",
  "emergencyContact": "+20 111 222 3333",
  "dateOfBirth": "1995-03-15",
  "gender": 1
}
```

> `gender` values: `1` = Male, `2` = Female, `3` = Unspecified  
> All fields are optional — send only what you want to update.

**Response (200 OK):**

Wrapped in `ApiResponse<ProfileResponse>`.

---

### 4.3 Change Password

| Property   | Value                          |
| ---------- | ------------------------------ |
| **Method** | `PUT`                          |
| **URL**    | `/api/profile/change-password` |
| **Auth**   | Bearer Token                   |

**Request Body:**

```json
{
  "currentPassword": "OldPass123!",
  "newPassword": "NewSecurePass456!",
  "confirmPassword": "NewSecurePass456!"
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<object>` with success message.

---

### 4.4 Upload Profile Picture

| Property    | Value                          |
| ----------- | ------------------------------ |
| **Method**  | `POST`                         |
| **URL**     | `/api/profile/profile-picture` |
| **Auth**    | Bearer Token                   |
| **Content** | `multipart/form-data`          |

**Request Body:**

Form-data with key `file` containing the image file.

**Response (200 OK):**

Wrapped in `ApiResponse<object>` with updated `profilePictureUrl`.

---

## 5. Notifications

Base: `api/notifications`

All endpoints require authentication.

---

### 5.1 Get My Notifications

| Property   | Value                |
| ---------- | -------------------- |
| **Method** | `GET`                |
| **URL**    | `/api/notifications` |
| **Auth**   | Bearer Token         |

**Response (200 OK):**

```json
[
  {
    "id": 1,
    "type": 2,
    "title": "New Training Assigned",
    "message": "You have been assigned to Morning Swim Plan.",
    "isRead": false,
    "createdAt": "2025-07-28T06:00:00Z"
  }
]
```

> **Note:** This endpoint does NOT wrap in `ApiResponse<T>` — returns raw array.

---

### 5.2 Get Unread Count

| Property   | Value                             |
| ---------- | --------------------------------- |
| **Method** | `GET`                             |
| **URL**    | `/api/notifications/unread-count` |
| **Auth**   | Bearer Token                      |

**Response (200 OK):**

Returns integer count (e.g., `5`).

> **Note:** This endpoint does NOT wrap in `ApiResponse<T>`.

---

### 5.3 Mark Notification as Read

| Property   | Value                          |
| ---------- | ------------------------------ |
| **Method** | `PUT`                          |
| **URL**    | `/api/notifications/{id}/read` |
| **Auth**   | Bearer Token                   |

**Response:** `204 No Content`

---

### 5.4 Mark All as Read

| Property   | Value                         |
| ---------- | ----------------------------- |
| **Method** | `PUT`                         |
| **URL**    | `/api/notifications/read-all` |
| **Auth**   | Bearer Token                  |

**Response:** `204 No Content`

---

## 6. Coach Assignments (Admin Only)

Base: `api/athletes/{athleteId}/coach-assignments`

All endpoints require `Admin` role.

---

### 6.1 Assign Coach to Athlete

| Property   | Value                                         |
| ---------- | --------------------------------------------- |
| **Method** | `POST`                                        |
| **URL**    | `/api/athletes/{athleteId}/coach-assignments` |
| **Auth**   | Bearer Token (Admin)                          |

**Request Body:**

```json
{
  "coachId": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "domainId": 1
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<CoachAssignmentResponse>`:

```json
{
  "success": true,
  "message": "Coach assigned successfully.",
  "data": {
    "id": 10,
    "athleteId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "coachId": "c3d4e5f6-a7b8-9012-cdef-345678901234",
    "coachName": "Coach Ahmed",
    "domainId": 1,
    "domainName": "Swimming Academy",
    "assignedAt": "2025-07-28"
  },
  "statusCode": 200
}
```

---

### 6.2 Remove Coach Assignment

| Property   | Value                                              |
| ---------- | -------------------------------------------------- |
| **Method** | `DELETE`                                           |
| **URL**    | `/api/athletes/{athleteId}/coach-assignments/{id}` |
| **Auth**   | Bearer Token (Admin)                               |

**Response (200 OK):**

Wrapped in `ApiResponse<bool>` or similar.

---

## 7. Coach Notes (Coach Only)

Base: `api/athletes/{athleteId}/coach-notes`

All endpoints require `Coach` role.

---

### 7.1 Get Athlete Coach Notes

| Property   | Value                                   |
| ---------- | --------------------------------------- |
| **Method** | `GET`                                   |
| **URL**    | `/api/athletes/{athleteId}/coach-notes` |
| **Auth**   | Bearer Token (Coach)                    |

**Query Parameters:**

| Parameter  | Type | Default | Description        |
| ---------- | ---- | ------- | ------------------ |
| PageNumber | int  | 1       | Page number        |
| PageSize   | int  | 10      | Max 50 per request |

**Response (200 OK):**

Wrapped in `ApiResponse<PagedResponse<CoachNoteResponse>>`:

```json
{
  "success": true,
  "message": "Notes retrieved successfully.",
  "data": {
    "items": [
      {
        "id": 1,
        "athleteId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
        "coachId": "c3d4e5f6-a7b8-9012-cdef-345678901234",
        "coachName": "Coach Ahmed",
        "content": "Great improvement in freestyle technique this week.",
        "createdAt": "2025-07-25T10:00:00Z"
      }
    ],
    "pageNumber": 1,
    "pageSize": 10,
    "totalCount": 5,
    "totalPages": 1,
    "hasPrevious": false,
    "hasNext": false
  },
  "statusCode": 200
}
```

---

## 8. Groups (Admin & Coach)

Base: `api/groups`

Requires `Admin` OR `Coach` role.

---

### 8.1 Add Athlete to Group

| Property   | Value                           |
| ---------- | ------------------------------- |
| **Method** | `POST`                          |
| **URL**    | `/api/groups/{id:int}/athletes` |
| **Auth**   | Bearer Token (Admin/Coach)      |

**Request Body:**

```json
{
  "athleteId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<AthleteGroupResponse>`:

```json
{
  "success": true,
  "message": "Athlete added to group successfully.",
  "data": {
    "athleteId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "athleteFullName": "John Doe",
    "groupId": 3,
    "groupName": "Advanced Swimmers",
    "joinedAt": "2025-07-28"
  },
  "statusCode": 200
}
```

---

## 9. Exercises (Coach Only)

Base: `api/exercises`

All endpoints require `Coach` role.

---

### 9.1 Create Exercise / Training Plan

| Property   | Value                |
| ---------- | -------------------- |
| **Method** | `POST`               |
| **URL**    | `/api/exercises`     |
| **Auth**   | Bearer Token (Coach) |

**Request Body:**

```json
{
  "title": "Morning Freestyle Drills",
  "description": "Focus on breathing and arm rotation.",
  "planExercises": [
    {
      "exerciseId": 1,
      "sets": 4,
      "reps": 10,
      "duration": 0,
      "intensity": 2,
      "orderIndex": 1,
      "notes": "Keep head down",
      "restSeconds": 60
    }
  ]
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`.

---

### 9.2 Get All Exercises (Paged)

| Property   | Value                |
| ---------- | -------------------- |
| **Method** | `GET`                |
| **URL**    | `/api/exercises`     |
| **Auth**   | Bearer Token (Coach) |

**Query Parameters:**

| Parameter       | Type   | Default | Description              |
| --------------- | ------ | ------- | ------------------------ |
| PageNumber      | int    | 1       | Page number              |
| PageSize        | int    | 10      | Max 50 per request       |
| Search          | string | null    | Search by title          |
| IncludeArchived | bool   | false   | Include archived items   |
| OnlyArchived    | bool   | false   | Show only archived items |

**Response (200 OK):**

Wrapped in `ApiResponse<PagedResponse<TrainingPlanResponse>>`.

---

### 9.3 Get Exercise by ID

| Property   | Value                     |
| ---------- | ------------------------- |
| **Method** | `GET`                     |
| **URL**    | `/api/exercises/{id:int}` |
| **Auth**   | Bearer Token (Coach)      |

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`:

```json
{
  "success": true,
  "message": "Exercise retrieved successfully.",
  "data": {
    "id": 5,
    "title": "Morning Freestyle Drills",
    "description": "Focus on breathing and arm rotation.",
    "isArchived": false,
    "createdById": "c3d4e5f6-a7b8-9012-cdef-345678901234",
    "createdAt": "2025-07-20T08:00:00Z",
    "updatedAt": "2025-07-25T10:00:00Z",
    "planExercises": [
      {
        "exerciseId": 1,
        "exerciseName": "Freestyle Stroke",
        "sets": 4,
        "reps": 10,
        "duration": 0,
        "intensity": "Medium",
        "orderIndex": 1,
        "notes": "Keep head down"
      }
    ]
  },
  "statusCode": 200
}
```

---

### 9.4 Update Exercise

| Property   | Value                     |
| ---------- | ------------------------- |
| **Method** | `PUT`                     |
| **URL**    | `/api/exercises/{id:int}` |
| **Auth**   | Bearer Token (Coach)      |

**Request Body:** Same as Create.

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`.

---

### 9.5 Archive Exercise

| Property   | Value                             |
| ---------- | --------------------------------- |
| **Method** | `PATCH`                           |
| **URL**    | `/api/exercises/{id:int}/archive` |
| **Auth**   | Bearer Token (Coach)              |

**Response (200 OK):**

Wrapped in `ApiResponse<bool>`.

---

### 9.6 Restore Exercise

| Property   | Value                             |
| ---------- | --------------------------------- |
| **Method** | `PATCH`                           |
| **URL**    | `/api/exercises/{id:int}/restore` |
| **Auth**   | Bearer Token (Coach)              |

**Response (200 OK):**

Wrapped in `ApiResponse<bool>`.

---

## 10. Training Plans (Coach Only)

Base: `api/training-plans`

All endpoints require `Coach` role.

> **Note:** The DTOs used here are identical to the Exercises endpoints (`TrainingPlanResponse`, `CreateTrainingPlanRequest`, `UpdateTrainingPlanRequest`, `TrainingPlanQueryParameters`). These represent Swimming Templates / Training Plans.

---

### 10.1 Create Training Plan

| Property   | Value                 |
| ---------- | --------------------- |
| **Method** | `POST`                |
| **URL**    | `/api/training-plans` |
| **Auth**   | Bearer Token (Coach)  |

**Request Body:**

```json
{
  "title": "Advanced Butterfly Training",
  "description": "Butterfly stroke technique and endurance.",
  "planExercises": [
    {
      "exerciseId": 2,
      "sets": 5,
      "reps": 8,
      "duration": 0,
      "intensity": 3,
      "orderIndex": 1,
      "notes": "Focus on dolphin kick",
      "restSeconds": 90
    }
  ]
}
```

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`.

---

### 10.2 Get Training Plans (Paged)

| Property   | Value                 |
| ---------- | --------------------- |
| **Method** | `GET`                 |
| **URL**    | `/api/training-plans` |
| **Auth**   | Bearer Token (Coach)  |

**Query Parameters:** Same as Exercises (`Search`, `IncludeArchived`, `OnlyArchived`, `PageNumber`, `PageSize`).

**Response (200 OK):**

Wrapped in `ApiResponse<PagedResponse<TrainingPlanResponse>>`.

---

### 10.3 Get Training Plan by ID

| Property   | Value                          |
| ---------- | ------------------------------ |
| **Method** | `GET`                          |
| **URL**    | `/api/training-plans/{id:int}` |
| **Auth**   | Bearer Token (Coach)           |

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`.

---

### 10.4 Update Training Plan

| Property   | Value                          |
| ---------- | ------------------------------ |
| **Method** | `PUT`                          |
| **URL**    | `/api/training-plans/{id:int}` |
| **Auth**   | Bearer Token (Coach)           |

**Request Body:** Same as Create.

**Response (200 OK):**

Wrapped in `ApiResponse<TrainingPlanResponse>`.

---

### 10.5 Archive Training Plan

| Property   | Value                                  |
| ---------- | -------------------------------------- |
| **Method** | `PATCH`                                |
| **URL**    | `/api/training-plans/{id:int}/archive` |
| **Auth**   | Bearer Token (Coach)                   |

**Response (200 OK):**

Wrapped in `ApiResponse<bool>`.

---

### 10.6 Restore Training Plan

| Property   | Value                                  |
| ---------- | -------------------------------------- |
| **Method** | `PATCH`                                |
| **URL**    | `/api/training-plans/{id:int}/restore` |
| **Auth**   | Bearer Token (Coach)                   |

**Response (200 OK):**

Wrapped in `ApiResponse<bool>`.

---

## 12. Test

Base: `api/test`

---

### 12.1 Send Test Email

| Property   | Value             |
| ---------- | ----------------- |
| **Method** | `POST`            |
| **URL**    | `/api/test/email` |
| **Auth**   | None              |

**Response (200 OK):**

```
Email sent successfully.
```

> **Note:** Returns plain text, not JSON.

---

### 12.2 Confirm Email

| Property   | Value                     |
| ---------- | ------------------------- |
| **Method** | `GET`                     |
| **URL**    | `/api/test/confirm-email` |
| **Auth**   | None (AllowAnonymous)     |

**Query Parameters:**

| Parameter | Type   | Required | Description        |
| --------- | ------ | -------- | ------------------ |
| email     | string | Yes      | User email address |
| token     | string | Yes      | Confirmation token |

**Response (200 OK):**

Returns HTML page with confirmation result.

---

## Summary

| Endpoint Group            | Required Role       |
| ------------------------- | ------------------- |
| `api/auth/*`              | None (except `/me`) |
| `api/users/*`             | Admin               |
| `api/profile/*`           | Any authenticated   |
| `api/notifications/*`     | Any authenticated   |
| `api/athletes/*/coach-*`  | Admin / Coach       |
| `api/groups/*`            | Admin or Coach      |
| `api/exercises/*`         | Coach               |
| `api/training-plans/*`    | Coach               |
| `api/training-sessions/*` | Admin or Coach      |
| `api/test/*`              | None                |

---

## Error Responses

All errors are returned via the `ExceptionMiddleware` and wrapped in `ApiResponse<T>.ErrorResponse`:

```json
{
  "success": false,
  "message": "User not found.",
  "data": null,
  "statusCode": 404
}
```

Common status codes:

- `400` — Bad Request / Validation Error
- `401` — Unauthorized (missing/invalid token)
- `403` — Forbidden (insufficient role)
- `404` — Not Found
- `409` — Conflict
- `500` — Internal Server Error

---

_Generated from source code — AquaMetrics API_
