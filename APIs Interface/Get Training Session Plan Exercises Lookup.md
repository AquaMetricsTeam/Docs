# Get Training Session Plan Exercises Lookup

## Endpoint

```http
GET /api/training-sessions/{trainingSessionId}/plan-exercises/lookup
```

## Description

Retrieves a lookup list of all `PlanExercise` records associated with the `TrainingPlan` assigned to the specified `TrainingSession`.

The client only needs to provide the `TrainingSessionId`. The API resolves the related `TrainingPlanId` internally and returns its plan exercises.

The requested training session must belong to the currently authenticated coach and the current domain.

---

## Authorization

**Authentication:** Required

**Required Role:** Swimming Coach / Fitness Coach

The endpoint validates that:

- The `TrainingSession` exists.
- The `TrainingSession` belongs to the authenticated coach.
- The `TrainingSession` belongs to the authenticated coach's domain.

---

## Route Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `trainingSessionId` | `int` | Yes | The ID of the training session whose plan exercises should be retrieved. |

### Example

```http
GET /api/training-sessions/15/plan-exercises/lookup
```

---

## Request Headers

```http
Authorization: Bearer {access_token}
```

---

## Request Body

No request body is required.

---

## Response

### Success — `200 OK`

```json
{
  "success": true,
  "message": "Plan exercises retrieved successfully.",
  "data": [
    {
      "id": 1,
      "exerciseId": 15,
      "exerciseName": "Bench Press",
      "sets": 4,
      "reps": 10,
      "duration": null,
      "orderIndex": 1
    },
    {
      "id": 2,
      "exerciseId": 21,
      "exerciseName": "Squat",
      "sets": 3,
      "reps": 12,
      "duration": null,
      "orderIndex": 2
    }
  ]
}
```

---

## Response Fields

| Field | Type | Description |
|---|---|---|
| `id` | `int` | The `PlanExercise` ID. |
| `exerciseId` | `int` | The referenced `Exercise` ID. |
| `exerciseName` | `string` | The name of the referenced exercise. |
| `sets` | `int` | Number of planned sets. |
| `reps` | `int` | Number of planned repetitions. |
| `duration` | `int/null` | Planned exercise duration, if applicable. |
| `orderIndex` | `int` | The exercise's order within the training plan. |

The results are ordered by `orderIndex`.

---

## Error Responses

### `401 Unauthorized`

The user is not authenticated.

```json
{
  "success": false,
  "message": "Unauthorized."
}
```

### `404 Not Found`

The specified training session does not exist or does not belong to the current coach/domain.

```json
{
  "success": false,
  "message": "Training session with ID 15 was not found."
}
```

### `500 Internal Server Error`

An unexpected server error occurred.

---

## Business Flow

```text
TrainingSessionId
       │
       ▼
Validate Training Session
       │
       ├── CoachId = Current User
       └── DomainId = Current Domain
       │
       ▼
Get TrainingPlanId
       │
       ▼
Get PlanExercises
       │
       ▼
Include Exercise
       │
       ▼
Map to Lookup Response
       │
       ▼
Return ordered list
```

---

## Frontend Usage

The frontend should call this endpoint when the user selects a training session and needs to display the exercises included in that session's training plan.

The frontend does **not** need to know or send the `TrainingPlanId`.

Example:

```text
Coach selects Training Session #15
             ↓
GET /api/training-sessions/15/plan-exercises/lookup
             ↓
API resolves TrainingPlanId
             ↓
Returns PlanExercise lookup
```