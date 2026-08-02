# AquaMetrics Training Module API Documentation

# Coach Assignments API Documentation

## Base Route

api/athletes/{athleteId:Guid}/coach-assignments

## Authorization

**Required Role:** `Admin`  
All endpoints require authentication via Bearer token and the user must have the `Admin` role.

---

## Endpoints

### 1. Assign Coach to Athlete

**POST** `/api/athletes/{athleteId}/coach-assignments`

Assigns a coach to a specific athlete.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

**Route Parameters:**
| Field | Type | Description |
|-------|------|-------------|
| `athleteId` | `Guid` | Athlete ID |

**Body:** `AssignCoachRequest`
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `coachId` | `Guid` | ✅ Yes | Coach ID to assign |

#### Response

**Status:** `200 OK`

**Body:** `ApiResponse<bool>`

````json
{
  "success": true,
  "message": null,
  "data": true
}

2. Remove Coach Assignment
DELETE /api/athletes/{athleteId}/coach-assignments/{assignmentId}
Removes a specific coach assignment from an athlete.
Request
Headers:
Authorization: Bearer token (required)

| Field          | Type   | Description             |
| -------------- | ------ | ----------------------- |
| `athleteId`    | `Guid` | Athlete ID              |
| `assignmentId` | `int`  | Assignment ID to remove |






## 1. POST /api/training-plans

### Description

Creates a new training template.

This endpoint supports **two UX workflows**:

1.  **Create Draft Template** (no assignment object).
2.  **Create Template and Assign Immediately** (include the `assignment`
    object).

If `assignment` is omitted (or both lists are empty), only the template
is created.

### Authorization

`Coach`

### Request (Draft)

```json
{
  "title": "Upper Body Strength",
  "description": "General strength program",
  "planExercises": [
    {
      "exerciseId": 1,
      "sets": 3,
      "reps": 12,
      "duration": 10,
      "restSeconds": 30,
      "orderIndex": 1
    }
  ]
}
````

### Request (Create + Assign)

```json
{
  "title": "Upper Body Strength",
  "description": "General strength program",
  "planExercises": [
    {
      "exerciseId": 1,
      "sets": 3,
      "reps": 12,
      "duration": 10,
      "restSeconds": 30,
      "orderIndex": 1
    }
  ],
  "assignment": {
    "groupIds": [1],
    "athleteIds": ["xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"]
  }
}
```

### Success Response

```json
{
  "success": true,
  "message": "Training plan created successfully.",
  "data": {
    "id": 5,
    "title": "Upper Body Strength",
    "description": "General strength program",
    "estimatedDurationMinutes": 10,
    "planExercises": [
      {
        "exerciseId": 1,
        "exerciseName": "Push Ups",
        "sets": 3,
        "reps": 12,
        "duration": 10,
        "restSeconds": 30,
        "orderIndex": 1
      }
    ]
  }
}
```

---

## 2. GET /api/training-plans

### Description

Returns paginated training templates.

### Authorization

`Coach`

### Query Parameters

- pageNumber
- pageSize
- search
- isArchived

### Success Response

```json
{
  "success": true,
  "data": {
    "items": [],
    "pageNumber": 1,
    "pageSize": 10,
    "totalCount": 25
  }
}
```

---

## 3. GET /api/training-plans/{id}

### Description

Returns one training template with all exercises.

### Authorization

`Coach`

### Success Response

```json
{
  "success": true,
  "data": {
    "id": 5,
    "title": "Upper Body Strength",
    "description": "General strength program",
    "estimatedDurationMinutes": 10,
    "planExercises": []
  }
}
```

---

## 4. PUT /api/training-plans/{id}

### Description

Updates a training template and its exercises.

### Authorization

`Coach`

Request body is the same structure as Create (without assignment).

### Success Response

```json
{
  "success": true,
  "message": "Training plan updated successfully.",
  "data": {}
}
```

---

## 5. PATCH /api/training-plans/{id}/archive

Archives a training template.

### Response

```json
{
  "success": true,
  "data": true
}
```

---

## 6. PATCH /api/training-plans/{id}/restore

Restores an archived template.

### Response

```json
{
  "success": true,
  "data": true
}
```

---

# Training Plan Assignments

## 1. POST /api/training-plan-assignments

Assigns an existing template to athletes and/or groups.

### Authorization

`Coach`

### Request

```json
{
  "trainingPlanId": 5,
  "groupIds": [1, 2],
  "athleteIds": ["xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"]
}
```

### Success Response

```json
{
  "success": true,
  "message": "Training plan assigned successfully.",
  "data": true
}
```

---

## 2. GET /api/training-plan-assignments/training-plan/{trainingPlanId}

Returns every assignment belonging to one training template.

### Success Response

```json
{
  "success": true,
  "data": [
    {
      "id": 12,
      "trainingPlanTitle": "Upper Body Strength",
      "assignedTo": "Sharks Group",
      "status": "Assigned",
      "assignedAt": "2026-07-30T09:00:00"
    }
  ]
}
```

---

## 3. DELETE /api/training-plan-assignments/{assignmentId}

Deletes one assignment.

### Success Response

```json
{
  "success": true,
  "message": "Assignment deleted successfully.",
  "data": true
}
```

---

# Training Sessions

## 1. POST /api/training-sessions

Creates a scheduled training session from a training template.

### Authorization

`Coach`

### Request

```json
{
  "title": "Morning Strength Session",
  "description": "Weekly workout",
  "trainingPlanId": 5,
  "sessionDate": "2026-08-01",
  "startTime": "08:00:00",
  "endTime": "09:30:00",
  "location": "Main Gym",
  "notes": "Bring resistance bands"
}
```

### Success Response

```json
{
  "success": true,
  "message": "Training session created successfully.",
  "data": {
    "id": 7,
    "title": "Morning Strength Session",
    "trainingPlanId": 5,
    "trainingPlanTitle": "Upper Body Strength",
    "sessionDate": "2026-08-01",
    "startTime": "08:00:00",
    "endTime": "09:30:00",
    "location": "Main Gym",
    "notes": "Bring resistance bands"
  }
}
```

> The backend automatically resolves all athletes assigned to the
> selected template (individuals and groups), removes duplicates, and
> sends email notifications.

---

## 2. GET /api/training-sessions

Returns paginated training sessions.

---

## 3. GET /api/training-sessions/{id}

Returns one training session.

---

## 4. PUT /api/training-sessions/{id}

Updates an existing training session.

---

## 5. DELETE /api/training-sessions/{id}

Archives the training session (soft delete).
