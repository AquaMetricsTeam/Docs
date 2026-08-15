# Athlete Overview API

## Overview

The Athlete Overview API retrieves a complete overview of a specific athlete, including:

- Basic athlete information
- Registration status
- Emergency and medical information
- Assigned groups
- Assigned coaches
- Swimming sessions
- Fitness sessions
- Training plan information
- Training record availability for each session

All endpoints require authentication.

---

## Get Athlete Overview

Retrieves the complete overview of a specific athlete.

### Endpoint

```http
GET /api/athletes/{athleteId}/overview
```

### Authorization

```http
Authorization: Bearer {accessToken}
```

**Authentication:** Required

### Route Parameters

| Parameter   | Type   | Required | Description                      |
| ----------- | ------ | -------- | -------------------------------- |
| `athleteId` | `Guid` | Yes      | Unique identifier of the athlete |

### Example Request

```http
GET /api/athletes/7f3e8c2a-91d4-4f5e-b6a1-123456789abc/overview
Authorization: Bearer eyJhbGciOi...
```

---

## Response

### Success Response

**Status Code:** `200 OK`

```json
{
  "data": {
    "id": "7f3e8c2a-91d4-4f5e-b6a1-123456789abc",
    "fullName": "Ahmed Mohamed",
    "email": "ahmed@example.com",
    "profilePictureUrl": "https://example.com/profile.jpg",
    "gender": "Male",
    "dateOfBirth": "2008-05-15",
    "age": 18,
    "registrationStatus": "Active",
    "emergencyContact": "+201001234567",
    "medicalNotes": "No known medical conditions",
    "groups": [
      {
        "id": 1,
        "name": "Junior Elite",
        "domainId": 1,
        "domainName": "Swimming"
      }
    ],
    "coaches": [
      {
        "coachId": "b12c45d6-7890-4abc-def1-234567890abc",
        "coachName": "Mohamed Ali",
        "profilePictureUrl": "https://example.com/coach.jpg",
        "domainId": 1,
        "domainName": "Swimming"
      }
    ],
    "swimmingSessions": [
      {
        "id": 101,
        "title": "Morning Swimming Session",
        "description": "Endurance and technique training",
        "sessionDate": "2026-08-15",
        "startTime": "08:00:00",
        "endTime": "10:00:00",
        "location": "Main Pool",
        "notes": "Focus on freestyle technique",
        "trainingPlanId": 25,
        "trainingPlanTitle": "Advanced Endurance Plan",
        "coachId": "b12c45d6-7890-4abc-def1-234567890abc",
        "coachName": "Mohamed Ali",
        "hasTrainingRecord": true
      }
    ],
    "fitnessSessions": [
      {
        "id": 201,
        "title": "Strength Training",
        "description": "Lower body strength session",
        "sessionDate": "2026-08-15",
        "startTime": "16:00:00",
        "endTime": "17:00:00",
        "location": "Fitness Hall",
        "notes": null,
        "trainingPlanId": 32,
        "trainingPlanTitle": "Swimming Strength Program",
        "coachId": "c23d56e7-8901-4bcd-ef12-345678901bcd",
        "coachName": "Omar Hassan",
        "hasTrainingRecord": false
      }
    ]
  }
}
```

> **Note:** The exact values of enum properties such as `Gender` and `RegistrationStatus` depend on their definitions and JSON serialization configuration.

---

# Response Model

## AthleteOverviewResponse

| Property             | Type                                                    | Nullable | Description                                   |
| -------------------- | ------------------------------------------------------- | -------- | --------------------------------------------- |
| `id`                 | `Guid`                                                  | No       | Unique identifier of the athlete              |
| `fullName`           | `string`                                                | No       | Athlete's full name                           |
| `email`              | `string`                                                | No       | Athlete's email address                       |
| `profilePictureUrl`  | `string`                                                | Yes      | URL of the athlete's profile picture          |
| `gender`             | `Gender`                                                | No       | Athlete's gender                              |
| `dateOfBirth`        | `DateOnly`                                              | No       | Athlete's date of birth                       |
| `age`                | `int`                                                   | No       | Athlete's current age                         |
| `registrationStatus` | `RegistrationStatus`                                    | No       | Current registration status                   |
| `emergencyContact`   | `string`                                                | Yes      | Emergency contact information                 |
| `medicalNotes`       | `string`                                                | Yes      | Medical notes associated with the athlete     |
| `groups`             | `IReadOnlyList<AthleteOverviewGroupResponse>`           | No       | Groups assigned to the athlete                |
| `coaches`            | `IReadOnlyList<AthleteOverviewCoachResponse>`           | No       | Coaches assigned to the athlete               |
| `swimmingSessions`   | `IReadOnlyList<AthleteOverviewSwimmingSessionResponse>` | No       | Swimming sessions associated with the athlete |
| `fitnessSessions`    | `IReadOnlyList<AthleteOverviewFitnessSessionResponse>`  | No       | Fitness sessions associated with the athlete  |

---

## AthleteOverviewGroupResponse

Represents a group associated with the athlete.

| Property     | Type     | Nullable | Description                    |
| ------------ | -------- | -------- | ------------------------------ |
| `id`         | `int`    | No       | Unique identifier of the group |
| `name`       | `string` | No       | Group name                     |
| `domainId`   | `int`    | No       | Identifier of the domain       |
| `domainName` | `string` | No       | Name of the domain             |

### Example

```json
{
  "id": 1,
  "name": "Junior Elite",
  "domainId": 1,
  "domainName": "Swimming"
}
```

---

## AthleteOverviewCoachResponse

Represents a coach assigned to the athlete.

| Property            | Type     | Nullable | Description                        |
| ------------------- | -------- | -------- | ---------------------------------- |
| `coachId`           | `Guid`   | No       | Unique identifier of the coach     |
| `coachName`         | `string` | No       | Coach's full name                  |
| `profilePictureUrl` | `string` | Yes      | URL of the coach's profile picture |
| `domainId`          | `int`    | No       | Coach's domain identifier          |
| `domainName`        | `string` | No       | Coach's domain name                |

### Example

```json
{
  "coachId": "b12c45d6-7890-4abc-def1-234567890abc",
  "coachName": "Mohamed Ali",
  "profilePictureUrl": "https://example.com/coach.jpg",
  "domainId": 1,
  "domainName": "Swimming"
}
```

---

## AthleteOverviewSwimmingSessionResponse

Represents a swimming training session associated with the athlete.

| Property            | Type       | Nullable | Description                                                |
| ------------------- | ---------- | -------- | ---------------------------------------------------------- |
| `id`                | `int`      | No       | Unique identifier of the session                           |
| `title`             | `string`   | No       | Session title                                              |
| `description`       | `string`   | Yes      | Session description                                        |
| `sessionDate`       | `DateOnly` | No       | Date of the session                                        |
| `startTime`         | `TimeOnly` | No       | Session start time                                         |
| `endTime`           | `TimeOnly` | No       | Session end time                                           |
| `location`          | `string`   | Yes      | Session location                                           |
| `notes`             | `string`   | Yes      | Additional session notes                                   |
| `trainingPlanId`    | `int`      | No       | Identifier of the associated training plan                 |
| `trainingPlanTitle` | `string`   | No       | Title of the associated training plan                      |
| `coachId`           | `Guid`     | No       | Identifier of the coach conducting the session             |
| `coachName`         | `string`   | No       | Name of the coach conducting the session                   |
| `hasTrainingRecord` | `bool`     | No       | Indicates whether a training record exists for the session |

### Example

```json
{
  "id": 101,
  "title": "Morning Swimming Session",
  "description": "Endurance and technique training",
  "sessionDate": "2026-08-15",
  "startTime": "08:00:00",
  "endTime": "10:00:00",
  "location": "Main Pool",
  "notes": "Focus on freestyle technique",
  "trainingPlanId": 25,
  "trainingPlanTitle": "Advanced Endurance Plan",
  "coachId": "b12c45d6-7890-4abc-def1-234567890abc",
  "coachName": "Mohamed Ali",
  "hasTrainingRecord": true
}
```

---

## AthleteOverviewFitnessSessionResponse

Represents a fitness training session associated with the athlete.

| Property            | Type       | Nullable | Description                                                |
| ------------------- | ---------- | -------- | ---------------------------------------------------------- |
| `id`                | `int`      | No       | Unique identifier of the session                           |
| `title`             | `string`   | No       | Session title                                              |
| `description`       | `string`   | Yes      | Session description                                        |
| `sessionDate`       | `DateOnly` | No       | Date of the session                                        |
| `startTime`         | `TimeOnly` | No       | Session start time                                         |
| `endTime`           | `TimeOnly` | No       | Session end time                                           |
| `location`          | `string`   | Yes      | Session location                                           |
| `notes`             | `string`   | Yes      | Additional session notes                                   |
| `trainingPlanId`    | `int`      | No       | Identifier of the associated training plan                 |
| `trainingPlanTitle` | `string`   | No       | Title of the associated training plan                      |
| `coachId`           | `Guid`     | No       | Identifier of the fitness coach conducting the session     |
| `coachName`         | `string`   | No       | Name of the fitness coach conducting the session           |
| `hasTrainingRecord` | `bool`     | No       | Indicates whether a training record exists for the session |

### Example

```json
{
  "id": 201,
  "title": "Strength Training",
  "description": "Lower body strength session",
  "sessionDate": "2026-08-15",
  "startTime": "16:00:00",
  "endTime": "17:00:00",
  "location": "Fitness Hall",
  "notes": null,
  "trainingPlanId": 32,
  "trainingPlanTitle": "Swimming Strength Program",
  "coachId": "c23d56e7-8901-4bcd-ef12-345678901bcd",
  "coachName": "Omar Hassan",
  "hasTrainingRecord": false
}
```

---

# Error Responses

## Athlete Not Found

**Status Code:** `404 Not Found`

Returned when the provided `athleteId` does not belong to an existing athlete.

Example:

```json
{
  "data": null,
  "message": "Athlete not found."
}
```

## Unauthorized

**Status Code:** `401 Unauthorized`

Returned when the request does not contain a valid authentication token.

```json
{
  "message": "Unauthorized"
}
```

## Invalid Athlete ID

**Status Code:** `400 Bad Request`

Returned when the `athleteId` route parameter is not a valid GUID.

Example:

```http
GET /api/athletes/not-a-guid/overview
```

---

# Endpoint Summary

| Method | Endpoint                             | Authentication | Description                                   |
| ------ | ------------------------------------ | -------------- | --------------------------------------------- |
| `GET`  | `/api/athletes/{athleteId}/overview` | Required       | Retrieves the complete overview of an athlete |

---

# Data Included in the Overview

The endpoint provides a consolidated view of the athlete:

```text
Athlete
├── Personal Information
│   ├── Name
│   ├── Email
│   ├── Profile Picture
│   ├── Gender
│   ├── Date of Birth
│   ├── Age
│   └── Registration Status
│
├── Additional Information
│   ├── Emergency Contact
│   └── Medical Notes
│
├── Groups
│   └── Group + Domain Information
│
├── Coaches
│   └── Coach + Domain Information
│
├── Swimming Sessions
│   ├── Session Information
│   ├── Training Plan
│   ├── Coach
│   └── Training Record Status
│
└── Fitness Sessions
    ├── Session Information
    ├── Training Plan
    ├── Coach
    └── Training Record Status
```

# Athlete Overview — Additional APIs

These endpoints provide detailed athlete overview data separated by category, including:

- Swimming sessions
- Fitness sessions
- Training plans
- Performance and fatigue analytics

All endpoints require authentication.

---

# 1. Get Athlete Swimming Sessions

Retrieves all swimming training sessions associated with a specific athlete.

### Endpoint

```http
GET /api/athletes/{athleteId}/overview/swimming-sessions
```

### Authorization

```http
Authorization: Bearer {accessToken}
```

**Authentication:** Required

### Route Parameters

| Parameter   | Type   | Required | Description                      |
| ----------- | ------ | -------- | -------------------------------- |
| `athleteId` | `Guid` | Yes      | Unique identifier of the athlete |

### Example Request

```http
GET /api/athletes/7f3e8c2a-91d4-4f5e-b6a1-123456789abc/overview/swimming-sessions
Authorization: Bearer eyJhbGciOi...
```

### Success Response

**Status Code:** `200 OK`

```json
{
  "data": [
    {
      "id": 101,
      "title": "Morning Swimming Session",
      "description": "Endurance and technique training",
      "sessionDate": "2026-08-15",
      "startTime": "08:00:00",
      "endTime": "10:00:00",
      "location": "Main Pool",
      "notes": "Focus on freestyle technique",
      "trainingPlanId": 25,
      "trainingPlanTitle": "Advanced Endurance Plan",
      "coachId": "b12c45d6-7890-4abc-def1-234567890abc",
      "coachName": "Mohamed Ali",
      "hasTrainingRecord": true
    }
  ]
}
```

### Response Model — `AthleteOverviewSwimmingSessionResponse`

| Property            | Type       | Nullable | Description                                |
| ------------------- | ---------- | -------- | ------------------------------------------ |
| `id`                | `int`      | No       | Unique identifier of the swimming session  |
| `title`             | `string`   | No       | Session title                              |
| `description`       | `string`   | Yes      | Session description                        |
| `sessionDate`       | `DateOnly` | No       | Date of the session                        |
| `startTime`         | `TimeOnly` | No       | Session start time                         |
| `endTime`           | `TimeOnly` | No       | Session end time                           |
| `location`          | `string`   | Yes      | Session location                           |
| `notes`             | `string`   | Yes      | Additional session notes                   |
| `trainingPlanId`    | `int`      | No       | Associated training plan identifier        |
| `trainingPlanTitle` | `string`   | No       | Associated training plan title             |
| `coachId`           | `Guid`     | No       | Identifier of the coach                    |
| `coachName`         | `string`   | No       | Coach's name                               |
| `hasTrainingRecord` | `bool`     | No       | Indicates whether a training record exists |

---

# 2. Get Athlete Fitness Sessions

Retrieves all fitness training sessions associated with a specific athlete.

### Endpoint

```http
GET /api/athletes/{athleteId}/overview/fitness-sessions
```

### Authorization

```http
Authorization: Bearer {accessToken}
```

**Authentication:** Required

### Route Parameters

| Parameter   | Type   | Required | Description                      |
| ----------- | ------ | -------- | -------------------------------- |
| `athleteId` | `Guid` | Yes      | Unique identifier of the athlete |

### Example Request

```http
GET /api/athletes/7f3e8c2a-91d4-4f5e-b6a1-123456789abc/overview/fitness-sessions
Authorization: Bearer eyJhbGciOi...
```

### Success Response

**Status Code:** `200 OK`

```json
{
  "data": [
    {
      "id": 201,
      "title": "Strength Training",
      "description": "Lower body strength session",
      "sessionDate": "2026-08-15",
      "startTime": "16:00:00",
      "endTime": "17:00:00",
      "location": "Fitness Hall",
      "notes": null,
      "trainingPlanId": 32,
      "trainingPlanTitle": "Swimming Strength Program",
      "coachId": "c23d56e7-8901-4bcd-ef12-345678901bcd",
      "coachName": "Omar Hassan",
      "hasTrainingRecord": false
    }
  ]
}
```

### Response Model — `AthleteOverviewFitnessSessionResponse`

| Property            | Type       | Nullable | Description                                |
| ------------------- | ---------- | -------- | ------------------------------------------ |
| `id`                | `int`      | No       | Unique identifier of the fitness session   |
| `title`             | `string`   | No       | Session title                              |
| `description`       | `string`   | Yes      | Session description                        |
| `sessionDate`       | `DateOnly` | No       | Date of the session                        |
| `startTime`         | `TimeOnly` | No       | Session start time                         |
| `endTime`           | `TimeOnly` | No       | Session end time                           |
| `location`          | `string`   | Yes      | Session location                           |
| `notes`             | `string`   | Yes      | Additional session notes                   |
| `trainingPlanId`    | `int`      | No       | Associated training plan identifier        |
| `trainingPlanTitle` | `string`   | No       | Associated training plan title             |
| `coachId`           | `Guid`     | No       | Identifier of the coach                    |
| `coachName`         | `string`   | No       | Coach's name                               |
| `hasTrainingRecord` | `bool`     | No       | Indicates whether a training record exists |

---

# 3. Get Athlete Training Plans

Retrieves the training plans associated with a specific athlete.

### Endpoint

```http
GET /api/athletes/{athleteId}/overview/training-plans
```

### Authorization

```http
Authorization: Bearer {accessToken}
```

**Authentication:** Required

### Route Parameters

| Parameter   | Type   | Required | Description                      |
| ----------- | ------ | -------- | -------------------------------- |
| `athleteId` | `Guid` | Yes      | Unique identifier of the athlete |

### Example Request

```http
GET /api/athletes/7f3e8c2a-91d4-4f5e-b6a1-123456789abc/overview/training-plans
Authorization: Bearer eyJhbGciOi...
```

### Success Response

**Status Code:** `200 OK`

```json
{
  "data": [
    {
      "id": 25,
      "title": "Advanced Endurance Plan",
      "description": "Advanced swimming endurance program",
      "estimatedDurationMinutes": 90,
      "planSource": "Coach",
      "approvalStatus": "Approved",
      "domainId": 1,
      "domainName": "Swimming"
    }
  ]
}
```

> **Note:** The exact response properties depend on the implementation of `AthleteOverviewTrainingPlanResponse`. The example above should be adjusted if the DTO contains additional or different properties.

---

# 4. Get Athlete Performance

Retrieves performance and fatigue analytics for a specific athlete.

The response contains:

- Performance trend
- Fatigue trend
- Total sessions
- Completed sessions
- Injured sessions
- Average performance rating
- Average fatigue level

### Endpoint

```http
GET /api/athletes/{athleteId}/overview/performance
```

### Authorization

```http
Authorization: Bearer {accessToken}
```

**Authentication:** Required

### Route Parameters

| Parameter   | Type   | Required | Description                      |
| ----------- | ------ | -------- | -------------------------------- |
| `athleteId` | `Guid` | Yes      | Unique identifier of the athlete |

### Example Request

```http
GET /api/athletes/7f3e8c2a-91d4-4f5e-b6a1-123456789abc/overview/performance
Authorization: Bearer eyJhbGciOi...
```

### Success Response

**Status Code:** `200 OK`

```json
{
  "data": {
    "performanceTrend": [
      {
        "trainingRecordId": 501,
        "trainingSessionId": 101,
        "sessionDate": "2026-08-10",
        "sessionTitle": "Morning Swimming Session",
        "domainId": 1,
        "value": 8
      },
      {
        "trainingRecordId": 502,
        "trainingSessionId": 102,
        "sessionDate": "2026-08-12",
        "sessionTitle": "Endurance Session",
        "domainId": 1,
        "value": 9
      }
    ],
    "fatigueTrend": [
      {
        "trainingRecordId": 501,
        "trainingSessionId": 101,
        "sessionDate": "2026-08-10",
        "sessionTitle": "Morning Swimming Session",
        "domainId": 1,
        "value": 5
      },
      {
        "trainingRecordId": 502,
        "trainingSessionId": 102,
        "sessionDate": "2026-08-12",
        "sessionTitle": "Endurance Session",
        "domainId": 1,
        "value": 6
      }
    ],
    "totalSessions": 20,
    "completedSessions": 17,
    "injuredSessions": 1,
    "averagePerformanceRating": 8.2,
    "averageFatigueLevel": 5.4
  }
}
```

---

## Response Model — `AthleteOverviewPerformanceResponse`

| Property                   | Type                                                     | Description                                  |
| -------------------------- | -------------------------------------------------------- | -------------------------------------------- |
| `performanceTrend`         | `IReadOnlyList<AthleteOverviewPerformancePointResponse>` | Historical performance values                |
| `fatigueTrend`             | `IReadOnlyList<AthleteOverviewPerformancePointResponse>` | Historical fatigue values                    |
| `totalSessions`            | `int`                                                    | Total number of sessions                     |
| `completedSessions`        | `int`                                                    | Number of completed sessions                 |
| `injuredSessions`          | `int`                                                    | Number of sessions associated with an injury |
| `averagePerformanceRating` | `decimal`                                                | Average performance rating                   |
| `averageFatigueLevel`      | `decimal`                                                | Average fatigue level                        |

---

## Response Model — `AthleteOverviewPerformancePointResponse`

Represents a single point in the performance or fatigue trend.

| Property            | Type       | Description                                  |
| ------------------- | ---------- | -------------------------------------------- |
| `trainingRecordId`  | `int`      | Training record identifier                   |
| `trainingSessionId` | `int`      | Training session identifier                  |
| `sessionDate`       | `DateOnly` | Date of the training session                 |
| `sessionTitle`      | `string`   | Title of the training session                |
| `domainId`          | `int`      | Domain identifier                            |
| `value`             | `int`      | Performance or fatigue value for the session |

### Important

The same `AthleteOverviewPerformancePointResponse` structure is used for both:

- `performanceTrend`
- `fatigueTrend`

The meaning of `value` depends on which collection contains the point:

```text
performanceTrend → value represents performance rating

fatigueTrend     → value represents fatigue level
```

---

# Error Responses

## Athlete Not Found

**Status Code:** `404 Not Found`

Returned when the provided `athleteId` does not belong to an existing athlete.

```json
{
  "data": null,
  "message": "Athlete not found."
}
```

## Unauthorized

**Status Code:** `401 Unauthorized`

Returned when the request does not contain a valid authentication token.

```json
{
  "message": "Unauthorized"
}
```

## Invalid Athlete ID

**Status Code:** `400 Bad Request`

Returned when `athleteId` is not a valid GUID.

Example:

```http
GET /api/athletes/not-a-guid/overview/performance
```

---

# Endpoint Summary

| Method | Endpoint                                               | Response                                                | Description                                   |
| ------ | ------------------------------------------------------ | ------------------------------------------------------- | --------------------------------------------- |
| `GET`  | `/api/athletes/{athleteId}/overview/swimming-sessions` | `IReadOnlyList<AthleteOverviewSwimmingSessionResponse>` | Get athlete swimming sessions                 |
| `GET`  | `/api/athletes/{athleteId}/overview/fitness-sessions`  | `IReadOnlyList<AthleteOverviewFitnessSessionResponse>`  | Get athlete fitness sessions                  |
| `GET`  | `/api/athletes/{athleteId}/overview/training-plans`    | `IReadOnlyList<AthleteOverviewTrainingPlanResponse>`    | Get athlete training plans                    |
| `GET`  | `/api/athletes/{athleteId}/overview/performance`       | `AthleteOverviewPerformanceResponse`                    | Get athlete performance and fatigue analytics |

---

# Athlete Overview API Structure

The athlete overview endpoints can be grouped as follows:

```text
/api/athletes/{athleteId}/overview
│
├── GET /overview
│   └── Complete athlete overview
│
├── GET /overview/swimming-sessions
│   └── Swimming sessions
│
├── GET /overview/fitness-sessions
│   └── Fitness sessions
│
├── GET /overview/training-plans
│   └── Training plans
│
└── GET /overview/performance
    ├── Performance trend
    ├── Fatigue trend
    ├── Total sessions
    ├── Completed sessions
    └── Injured sessions
```
