# Training Records API

## Overview

The Training Records API is used by coaches to record and review an athlete's performance during a specific training session.

A training record contains:

- Overall session evaluation
- Fatigue level
- Session completion status
- Injury occurrence
- Coach comments
- Detailed performance for each planned exercise

### Base Route

```text
/api/training-record
```

### Authorization

All endpoints require authentication and the user must have the `Coach` role.

```csharp
[Authorize(Roles = Roles.Coach)]
```

---

# Endpoints

## 1. Create Training Record

### Request

```http
POST /api/training-record
```

### Description

Creates a training record for an athlete in a specific training session, including the actual performance of the planned exercises.

### Request Body

```json
{
  "athleteId": "2d166dff-5101-4278-f6bc-08deefecdca4",
  "trainingSessionId": 2,
  "performanceRating": 9,
  "fatigueLevel": 5,
  "sessionCompleted": true,
  "injuryOccurred": false,
  "overallComment": "Excellent session.",
  "exercisePerformances": [
    {
      "planExerciseId": 26,
      "completedSets": 4,
      "completedReps": 8,
      "completedDuration": null,
      "weightUsed": 40.8,
      "rpe": 7,
      "status": 1,
      "coachComment": "Good technique."
    }
  ]
}
```

### Request Fields

| Field | Type | Required | Description |
|---|---|---:|---|
| `athleteId` | `Guid` | Yes | Athlete who performed the session |
| `trainingSessionId` | `int` | Yes | Training session being recorded |
| `performanceRating` | `int` | Yes | Overall performance rating |
| `fatigueLevel` | `int` | Yes | Athlete's fatigue level |
| `sessionCompleted` | `bool` | Yes | Whether the session was completed |
| `injuryOccurred` | `bool` | Yes | Whether an injury occurred |
| `overallComment` | `string?` | No | Overall coach comment |
| `exercisePerformances` | `array` | Yes | Actual performance for the planned exercises |

### Exercise Performance Fields

| Field | Type | Required | Description |
|---|---|---:|---|
| `planExerciseId` | `int` | Yes | Planned exercise being recorded |
| `completedSets` | `int` | Yes | Number of sets actually completed |
| `completedReps` | `int` | Yes | Number of reps actually completed |
| `completedDuration` | `int?` | No | Actual duration, when applicable |
| `weightUsed` | `decimal?` | No | Weight used during the exercise |
| `rpe` | `int?` | No | Rate of Perceived Exertion |
| `status` | `PerformanceStatus` | Yes | Completion status |
| `coachComment` | `string?` | No | Coach's comment for the exercise |

### Response

```json
{
  "success": true,
  "message": "Training record created successfully.",
  "data": {
    "id": 1,
    "athleteId": "2d166dff-5101-4278-f6bc-08deefecdca4",
    "athleteName": "Ahmed Ali",
    "trainingSessionId": 2,
    "sessionTitle": "Training Session Test One",
    "sessionDate": "2026-08-15",
    "performanceRating": 9,
    "fatigueLevel": 5,
    "sessionCompleted": true,
    "injuryOccurred": false,
    "overallComment": "Excellent session.",
    "exercisePerformances": []
  }
}
```

---

# 2. Update Training Record

### Request

```http
PUT /api/training-record/{id}
```

### Description

Updates the overall evaluation and exercise performances of an existing training record.

The athlete and training session are not supplied in the request body.

### Example

```http
PUT /api/training-record/1
```

### Request Body

```json
{
  "performanceRating": 8,
  "fatigueLevel": 6,
  "sessionCompleted": true,
  "injuryOccurred": false,
  "overallComment": "Good session, slightly fatigued near the end.",
  "exercisePerformances": [
    {
      "planExerciseId": 26,
      "completedSets": 4,
      "completedReps": 8,
      "completedDuration": null,
      "weightUsed": 42.5,
      "rpe": 8,
      "status": 1,
      "coachComment": "Increased weight successfully."
    }
  ]
}
```

### Response

Returns `TrainingRecordDetailsResponse`.

---

# 3. Get Paged Training Records

### Request

```http
GET /api/training-record
```

### Description

Returns paginated training records with optional filtering, searching, and sorting.

### Query Parameters

| Parameter | Type | Description |
|---|---|---|
| `PageIndex` | `int` | Page number |
| `PageSize` | `int` | Number of records per page |
| `AthleteId` | `Guid?` | Filter by athlete |
| `TrainingSessionId` | `int?` | Filter by training session |
| `InjuryOccurred` | `bool?` | Filter records by injury occurrence |
| `SessionCompleted` | `bool?` | Filter by completion status |
| `MinPerformanceRating` | `int?` | Minimum performance rating |
| `MaxPerformanceRating` | `int?` | Maximum performance rating |
| `FromDate` | `DateOnly?` | Start session date |
| `ToDate` | `DateOnly?` | End session date |
| `Search` | `string?` | Search/filter text |
| `SortBy` | `TrainingRecordSortBy` | Field used for sorting |
| `Descending` | `bool` | Sort descending when `true` |

### Example

```http
GET /api/training-record?PageIndex=1&PageSize=10&MinPerformanceRating=7&MaxPerformanceRating=10&SessionCompleted=true&SortBy=Date&Descending=true
```

### Response

Returns:

```text
ApiResponse<PagedResponse<TrainingRecordResponse>>
```

Each record contains:

```json
{
  "id": 1,
  "athleteId": "2d166dff-5101-4278-f6bc-08deefecdca4",
  "athleteName": "Ahmed Ali",
  "trainingSessionId": 2,
  "sessionDate": "2026-08-15",
  "sessionTitle": "Training Session Test One",
  "performanceRating": 9,
  "fatigueLevel": 5,
  "sessionCompleted": true,
  "injuryOccurred": false
}
```

---

# 4. Get Training Record By ID

### Request

```http
GET /api/training-record/{id}
```

### Example

```http
GET /api/training-record/1
```

### Description

Returns the complete details of a specific training record, including all recorded exercise performances.

### Response

Returns:

```text
ApiResponse<TrainingRecordDetailsResponse>
```

The response includes:

- Athlete information
- Training session information
- Overall performance
- Fatigue
- Completion status
- Injury status
- Overall comment
- Detailed exercise performances

---

# 5. Get Training Records By Athlete

### Request

```http
GET /api/training-record/athlete/{athleteId}
```

### Example

```http
GET /api/training-record/athlete/2d166dff-5101-4278-f6bc-08deefecdca4?PageIndex=1&PageSize=10
```

### Description

Returns the training history of a specific athlete.

The same filtering, searching, and sorting parameters available in the paged endpoint can be supplied.

### Response

```text
ApiResponse<PagedResponse<TrainingRecordResponse>>
```

---

# 6. Get Training Records By Session

### Request

```http
GET /api/training-record/session/{trainingSessionId}
```

### Example

```http
GET /api/training-record/session/2
```

### Description

Returns all training records recorded for athletes in a specific training session.

### Response

```text
ApiResponse<List<TrainingRecordResponse>>
```

---

# 7. Get Session Training Record Status

### Request

```http
GET /api/training-record/session/{trainingSessionId}/status
```

### Example

```http
GET /api/training-record/session/2/status
```

### Description

Returns the training-record status of the athletes associated with a training session.

This endpoint is useful for the coach's session dashboard to determine which athletes already have a training record and which still need one.

### Response

```json
{
  "success": true,
  "data": [
    {
      "athleteId": "2d166dff-5101-4278-f6bc-08deefecdca4",
      "athleteName": "Ahmed Ali",
      "hasTrainingRecord": true,
      "trainingRecordId": 1
    },
    {
      "athleteId": "8f3a...",
      "athleteName": "Mohamed Hassan",
      "hasTrainingRecord": false,
      "trainingRecordId": null
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|---|---|---|
| `athleteId` | `Guid` | Athlete identifier |
| `athleteName` | `string` | Athlete name |
| `hasTrainingRecord` | `bool` | Whether a record has been created |
| `trainingRecordId` | `int?` | Existing record ID, or `null` |

---

# 8. Archive Training Record

### Request

```http
PATCH /api/training-record/{id}/archive
```

### Example

```http
PATCH /api/training-record/1/archive
```

### Description

Archives an existing training record without permanently deleting it.

### Response

```text
ApiResponse<bool>
```

---

# 9. Restore Training Record

### Request

```http
PATCH /api/training-record/{id}/restore
```

### Example

```http
PATCH /api/training-record/1/restore
```

### Description

Restores an archived training record.

### Response

```text
ApiResponse<bool>
```

---

# PerformanceStatus

The `PerformanceStatus` enum describes how the athlete performed the planned exercise.

```csharp
public enum PerformanceStatus
{
    Completed = 1,
    PartiallyCompleted = 2,
    Skipped = 3,
    Modified = 4
}
```

| Value | Name | Description |
|---:|---|---|
| `1` | `Completed` | Exercise was completed as planned |
| `2` | `PartiallyCompleted` | Exercise was only partially completed |
| `3` | `Skipped` | Exercise was not performed |
| `4` | `Modified` | Exercise was performed with modifications |

---

# Response Models

## TrainingRecordResponse

Used for list/paginated responses.

```csharp
public class TrainingRecordResponse
{
    public int Id { get; set; }
    public Guid AthleteId { get; set; }
    public string AthleteName { get; set; }
    public int TrainingSessionId { get; set; }
    public DateOnly SessionDate { get; set; }
    public string SessionTitle { get; set; }
    public int PerformanceRating { get; set; }
    public int FatigueLevel { get; set; }
    public bool SessionCompleted { get; set; }
    public bool InjuryOccurred { get; set; }
}
```

## TrainingRecordDetailsResponse

Used for detailed records.

```csharp
public class TrainingRecordDetailsResponse
{
    public int Id { get; set; }
    public Guid AthleteId { get; set; }
    public string AthleteName { get; set; }
    public int TrainingSessionId { get; set; }
    public string SessionTitle { get; set; }
    public DateOnly SessionDate { get; set; }
    public int PerformanceRating { get; set; }
    public int FatigueLevel { get; set; }
    public bool SessionCompleted { get; set; }
    public bool InjuryOccurred { get; set; }
    public string? OverallComment { get; set; }
    public List<ExercisePerformanceResponse> ExercisePerformances { get; set; }
}
```

## ExercisePerformanceResponse

```csharp
public class ExercisePerformanceResponse
{
    public int Id { get; set; }
    public int PlanExerciseId { get; set; }
    public int ExerciseId { get; set; }
    public string ExerciseTitle { get; set; }
    public int PlannedSets { get; set; }
    public int PlannedReps { get; set; }
    public int PlannedDuration { get; set; }
    public int CompletedSets { get; set; }
    public int CompletedReps { get; set; }
    public int? CompletedDuration { get; set; }
    public decimal? WeightUsed { get; set; }
    public int? RPE { get; set; }
    public PerformanceStatus Status { get; set; }
    public string? CoachComment { get; set; }
}
```

---

# Business Flow

A typical training-record workflow is:

```text
Training Plan
      ↓
Training Session
      ↓
Athlete Assigned To Plan
      ↓
Attendance Recorded
      ↓
Coach Creates Training Record
      ↓
Overall Session Evaluation
      +
Exercise Performances
      ↓
Training Record Stored
      ↓
Coach Can Review / Update
      ↓
Training History
```

Before creating a training record, the system validates that:

1. The training session exists and belongs to the current coach/domain.
2. The athlete is assigned to the training plan.
3. Attendance has been recorded.
4. The athlete is present or late.
5. A training record does not already exist for the athlete in that session.
6. Every submitted `PlanExerciseId` belongs to the training plan of the selected session.
7. Duplicate plan exercises are not submitted in the same request.

---

# Notes

- All coach/domain authorization is handled server-side.
- `AthleteId`, `TrainingSessionId`, and `PlanExerciseId` should reference existing entities.
- Training records are archived/restored rather than permanently deleted.
- `ExercisePerformance` records represent the **actual execution** of a planned exercise, while `PlanExercise` represents what was originally planned.
- `RPE` means **Rate of Perceived Exertion**, representing how difficult the exercise felt to the athlete.
