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

# Lookup Training Record

## Endpoint

```http
GET /api/training-record/Lookup
```

```response

```

```json
{
  "id": 1,
  "athleteId": "2d166dff-5101-4278-f6bc-08deefecdca4",
  "athleteName": "Ahmed Ali",
  "trainingSessionId": 2,
  "sessionDate": "2026-08-15",
  "sessionTitle": "Training Session Test One",
  "performanceRating": 9
}
```

# 1. Create Training Record

Creates a Training Record for an athlete in a specific Training Session.

## Endpoint

```http
POST /api/training-record
```

## Request Body

```json
{
  "athleteId": "GUID",
  "trainingSessionId": 1,
  "performanceRating": 4,
  "fatigueLevel": 3,
  "sessionCompleted": true,
  "injuryOccurred": false,
  "overallComment": "Good overall performance.",
  "exercisePerformances": [],
  "swimmingPerformances": []
}
```

### Common Fields

| Field                  | Type                               | Required | Description                                 |
| ---------------------- | ---------------------------------- | -------: | ------------------------------------------- |
| `athleteId`            | `Guid`                             |      Yes | Athlete whose performance is being recorded |
| `trainingSessionId`    | `int`                              |      Yes | Training Session associated with the record |
| `performanceRating`    | `int`                              |      Yes | Overall performance rating                  |
| `fatigueLevel`         | `int`                              |      Yes | Athlete's fatigue level                     |
| `sessionCompleted`     | `bool`                             |      Yes | Whether the athlete completed the session   |
| `injuryOccurred`       | `bool`                             |      Yes | Whether an injury occurred                  |
| `overallComment`       | `string?`                          |       No | General coach comment                       |
| `exercisePerformances` | `List<ExercisePerformanceRequest>` |  Fitness | Fitness performance data                    |
| `swimmingPerformances` | `List<SwimmingDrillRequest>`       | Swimming | Swimming performance data                   |

The frontend sends the appropriate performance list according to the training type.

## Fitness Example

```json
{
  "athleteId": "GUID",
  "trainingSessionId": 10,
  "performanceRating": 4,
  "fatigueLevel": 3,
  "sessionCompleted": true,
  "injuryOccurred": false,
  "overallComment": "Strong fitness session.",
  "exercisePerformances": [
    {
      "planExerciseId": 5,
      "completedSets": 4,
      "completedReps": 10,
      "completedDuration": null,
      "weightUsed": 60,
      "rpe": 7,
      "status": 1,
      "coachComment": "Good form."
    }
  ],
  "swimmingPerformances": []
}
```

## Swimming Example

```json
{
  "athleteId": "GUID",
  "trainingSessionId": 15,
  "performanceRating": 5,
  "fatigueLevel": 2,
  "sessionCompleted": true,
  "injuryOccurred": false,
  "overallComment": "Very good swimming session.",
  "exercisePerformances": [],
  "swimmingPerformances": [
    {
      "stroke": 1,
      "distanceMeters": 100,
      "repetitions": 4,
      "restIntervalSeconds": 30,
      "bestRepTime": "00:01:10",
      "averageRepTime": "00:01:13",
      "worstRepTime": "00:01:16",
      "technique": 4,
      "start": 5,
      "turns": 4,
      "finish": 4,
      "paceConsistency": 4,
      "rpe": 7,
      "status": 1,
      "coachComment": "Good pacing and turns."
    }
  ]
}
```

### Performance Status

```csharp
public enum PerformanceStatus
{
    Completed = 1,
    PartiallyCompleted = 2,
    Skipped = 3,
    Modified = 4
}
```

| Value | Status              |
| ----: | ------------------- |
|   `1` | Completed           |
|   `2` | Partially Completed |
|   `3` | Skipped             |
|   `4` | Modified            |

### Validation Rules

Before creating a Training Record, the API validates:

1. The Training Session exists and belongs to the current coach/domain.
2. The athlete is assigned to the Training Plan associated with the session.
3. Attendance exists for the athlete and session.
4. Attendance must be `Present` or `Late`.
5. The athlete cannot have another Training Record for the same session.
6. If `exercisePerformances` contains values, the exercises are validated against the Training Plan.

---

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

# 3. Get Paged Training Records for fitness coach

### Request

```http
GET /api/training-record
```

### Description

Returns paginated training records with optional filtering, searching, and sorting.

### Query Parameters

| Parameter              | Type                   | Description                         |
| ---------------------- | ---------------------- | ----------------------------------- |
| `PageIndex`            | `int`                  | Page number                         |
| `PageSize`             | `int`                  | Number of records per page          |
| `AthleteId`            | `Guid?`                | Filter by athlete                   |
| `TrainingSessionId`    | `int?`                 | Filter by training session          |
| `InjuryOccurred`       | `bool?`                | Filter records by injury occurrence |
| `SessionCompleted`     | `bool?`                | Filter by completion status         |
| `MinPerformanceRating` | `int?`                 | Minimum performance rating          |
| `MaxPerformanceRating` | `int?`                 | Maximum performance rating          |
| `FromDate`             | `DateOnly?`            | Start session date                  |
| `ToDate`               | `DateOnly?`            | End session date                    |
| `Search`               | `string?`              | Search/filter text                  |
| `SortBy`               | `TrainingRecordSortBy` | Field used for sorting              |
| `Descending`           | `bool`                 | Sort descending when `true`         |

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

| Field               | Type     | Description                       |
| ------------------- | -------- | --------------------------------- |
| `athleteId`         | `Guid`   | Athlete identifier                |
| `athleteName`       | `string` | Athlete name                      |
| `hasTrainingRecord` | `bool`   | Whether a record has been created |
| `trainingRecordId`  | `int?`   | Existing record ID, or `null`     |

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

| Value | Name                 | Description                               |
| ----: | -------------------- | ----------------------------------------- |
|   `1` | `Completed`          | Exercise was completed as planned         |
|   `2` | `PartiallyCompleted` | Exercise was only partially completed     |
|   `3` | `Skipped`            | Exercise was not performed                |
|   `4` | `Modified`           | Exercise was performed with modifications |

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
