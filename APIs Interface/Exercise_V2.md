# Exercises API Documentation

## Base Route

## Authorization

**Required Role:** `Coach`  
All endpoints require authentication via Bearer token and the user must have the `Coach` role.

---

## Endpoints

### 1. Create Exercise

**POST** `/api/exercises`

Creates a new exercise with optional muscle group and swimming category classification.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

**Body:** `CreateExerciseRequest`
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | `string` | ✅ Yes | Exercise title |
| `description` | `string?` | ❌ No | Exercise description |
| `muscleGroup` | `MuscleGroup?` | ❌ No | Target muscle group (see enum below) |
| `category` | `SwimmingExerciseCategory?` | ❌ No | Swimming category (see enum below) |

#### Request Example

```json
{
  "title": "Bench Press",
  "description": "Flat barbell bench press for chest development",
  "muscleGroup": 1,
  "category": null
}

{
  "title": "Freestyle Sprint 50m",
  "description": "Maximum effort freestyle sprint",
  "muscleGroup": null,
  "category": 1
}

```

#### Resposne

```json
{
  "success": true,
  "message": null,
  "data": {
    "id": 1,
    "title": "Bench Press",
    "description": "Flat barbell bench press for chest development",
    "muscleGroup": 1,
    "category": null,
    "isArchived": false,
    "createdAt": "2026-08-17T02:13:00Z",
    "updatedAt": "2026-08-17T02:13:00Z"
  }
}
```

### 2.Get All Exercises (Paged)

/api/exercises

Headers:
Authorization: Bearer token (required)
Query Parameters: ExerciseLookupQueryParameters

| Field             | Type                        | Required | Default | Description                                       |
| ----------------- | --------------------------- | -------- | ------- | ------------------------------------------------- |
| `page`            | `int`                       | ❌ No    | `1`     | Page number (inherited from PaginationRequest)    |
| `pageSize`        | `int`                       | ❌ No    | `20`    | Items per page (inherited from PaginationRequest) |
| `search`          | `string?`                   | ❌ No    | `null`  | Search by title                                   |
| `muscleGroup`     | `MuscleGroup?`              | ❌ No    | `null`  | Filter by muscle group                            |
| `category`        | `SwimmingExerciseCategory?` | ❌ No    | `null`  | Filter by swimming category                       |
| `includeArchived` | `bool`                      | ❌ No    | `false` | Include archived exercises                        |
| `onlyArchived`    | `bool`                      | ❌ No    | `false` | Show only archived exercises                      |

#### Response

```json
{
  "success": true,
  "message": null,
  "data": {
    "items": [
      {
        "id": 1,
        "title": "Bench Press",
        "description": "Flat barbell bench press",
        "muscleGroup": 1,
        "category": null,
        "isArchived": false,
        "createdAt": "2026-08-17T02:13:00Z",
        "updatedAt": "2026-08-17T02:13:00Z"
      },
      {
        "id": 2,
        "title": "Freestyle Sprint 50m",
        "description": "Maximum effort freestyle",
        "muscleGroup": null,
        "category": 1,
        "isArchived": false,
        "createdAt": "2026-08-17T02:13:00Z",
        "updatedAt": "2026-08-17T02:13:00Z"
      }
    ],
    "page": 1,
    "pageSize": 20,
    "totalCount": 2,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

### 3. Get Exercises Lookup

/api/exercises/exercises-lookup
Query Parameters: ExerciseLookupParameters
| Field | Type | Required | Description |
| ------------- | --------------------------- | -------- | --------------------------- |
| `search` | `string?` | ❌ No | Search by title |
| `muscleGroup` | `MuscleGroup?` | ❌ No | Filter by muscle group |
| `category` | `SwimmingExerciseCategory?` | ❌ No | Filter by swimming category |

#### Response

```json
{
  "success": true,
  "message": null,
  "data": [
    {
      "id": 1,
      "title": "Bench Press"
    },
    {
      "id": 2,
      "title": "Freestyle Sprint 50m"
    }
  ]
}
```

# Muscle Group

| Value | Name         | Description              |
| ----- | ------------ | ------------------------ |
| `1`   | `Chest`      | Chest muscles            |
| `2`   | `Back`       | Back muscles             |
| `3`   | `Shoulders`  | Shoulder muscles         |
| `4`   | `Biceps`     | Biceps                   |
| `5`   | `Triceps`    | Triceps                  |
| `6`   | `Forearms`   | Forearm muscles          |
| `7`   | `Quadriceps` | Quadriceps (front thigh) |
| `8`   | `Hamstrings` | Hamstrings (back thigh)  |
| `9`   | `Glutes`     | Gluteal muscles          |
| `10`  | `Calves`     | Calf muscles             |
| `11`  | `Core`       | Core/abdominal muscles   |
| `12`  | `Traps`      | Trapezius muscles        |
| `13`  | `LowerBack`  | Lower back muscles       |
| `14`  | `FullBody`   | Full body compound       |

# Swimming Exercise Category

| Value | Name               | Description                 |
| ----- | ------------------ | --------------------------- |
| `1`   | `Freestyle`        | Front crawl technique       |
| `2`   | `Backstroke`       | Backstroke technique        |
| `3`   | `Breaststroke`     | Breaststroke technique      |
| `4`   | `Butterfly`        | Butterfly technique         |
| `5`   | `Starts`           | Dive starts                 |
| `6`   | `Turns`            | Wall turns                  |
| `7`   | `Underwater`       | Underwater swimming         |
| `8`   | `Kicking`          | Kick-focused drills         |
| `9`   | `Pulling`          | Pull-focused drills         |
| `10`  | `Drills`           | Technique drills            |
| `11`  | `Technique`        | General technique work      |
| `12`  | `Breathing`        | Breathing exercises         |
| `13`  | `Endurance`        | Distance/endurance training |
| `14`  | `Sprint`           | Sprint training             |
| `15`  | `RacePace`         | Race pace training          |
| `16`  | `Aerobic`          | Aerobic capacity training   |
| `17`  | `Anaerobic`        | Anaerobic training          |
| `18`  | `IndividualMedley` | IM (all strokes)            |
| `19`  | `OpenWater`        | Open water swimming         |
| `20`  | `Recovery`         | Recovery/active rest        |
