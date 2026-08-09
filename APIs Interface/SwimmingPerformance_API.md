# Swimming Performance API

## Overview

The Swimming Performance API is used by authenticated users to record, review, update, and manage swimming-specific performance data linked to a training record.

A swimming performance represents a swimming drill/set and captures:

- Stroke or drill type
- Distance
- Repetitions
- Rest interval
- Best, average, and worst repetition times
- Technique evaluation
- Start evaluation
- Turn evaluation
- Finish evaluation
- Pace consistency
- RPE
- Performance status
- Coach comment

### Base Route

```text
/api/Swimming-Performance
```

### Authorization

All endpoints require authentication.

```csharp
[Authorize]
```

---

# Endpoints

# 1. Get Paged Training Records for swimming coach

### Request

```http
GET /api/Swimming-Performance/trainingRecord
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

````json
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


# 2. Update Swimming Performance

### Request

```http
PUT /api/Swimming-Performance/{id}
````

### Example

```http
PUT /api/Swimming-Performance/1
```

### Description

Updates an existing swimming performance entry.

### Request Body

```json
{
  "stroke": 1,
  "distanceMeters": 100,
  "repetitions": 4,
  "restIntervalSeconds": 25,
  "bestRepTime": "00:01:07",
  "averageRepTime": "00:01:09",
  "worstRepTime": "00:01:12",
  "technique": 4,
  "start": 4,
  "turns": 4,
  "finish": 4,
  "paceConsistency": 4,
  "rpe": 8,
  "status": 1,
  "coachComment": "Improved turns and pace consistency."
}
```

### Response

```text
ApiResponse<SwimmingPerformanceDetailsResponse>
```

---

# 3. Get Swimming Performance By ID

### Request

```http
GET /api/Swimming-Performance/{id}
```

### Example

```http
GET /api/Swimming-Performance/1
```

### Description

Returns the complete details of a specific swimming performance.

### Response

```text
ApiResponse<SwimmingPerformanceDetailsResponse>
```

---

# 4. Get Swimming Performances By Training Record

### Request

```http
GET /api/Swimming-Performance/training-record/{trainingRecordId}
```

### Example

```http
GET /api/Swimming-Performance/training-record/1
```

### Description

Returns all swimming performance entries associated with a specific training record.

This endpoint is useful when displaying the swimming details of a completed training session.

### Response

```text
ApiResponse<List<SwimmingPerformanceResponse>>
```

---

# 6. Archive Swimming Performance

### Request

```http
PATCH /api/Swimming-Performance/{id}/archive
```

### Example

```http
PATCH /api/Swimming-Performance/1/archive
```

### Description

Archives a swimming performance without permanently deleting it.

### Response

```text
ApiResponse<string>
```

---

# 7. Restore Swimming Performance

### Request

```http
PATCH /api/Swimming-Performance/{id}/restore
```

### Example

```http
PATCH /api/Swimming-Performance/1/restore
```

### Description

Restores an archived swimming performance.

### Response

```text
ApiResponse<string>
```

---

# Enums

## StrokeType

Defines the swimming stroke or swimming drill category.

```csharp
public enum StrokeType
{
    Freestyle = 1,
    Backstroke = 2,
    Breaststroke = 3,
    Butterfly = 4,
    IndividualMedley = 5,
    Kick = 6,
    Pull = 7,
    Drill = 8,
    Mixed = 9
}
```

| Value | Name               | Description           |
| ----: | ------------------ | --------------------- |
|   `1` | `Freestyle`        | Freestyle swimming    |
|   `2` | `Backstroke`       | Backstroke swimming   |
|   `3` | `Breaststroke`     | Breaststroke swimming |
|   `4` | `Butterfly`        | Butterfly swimming    |
|   `5` | `IndividualMedley` | Individual medley     |
|   `6` | `Kick`             | Kick-focused work     |
|   `7` | `Pull`             | Pull-focused work     |
|   `8` | `Drill`            | Technique/drill work  |
|   `9` | `Mixed`            | Mixed stroke work     |

---

## PerformanceStatus

Defines how the swimming performance was completed.

```csharp
public enum PerformanceStatus
{
    Completed = 1,
    PartiallyCompleted = 2,
    Skipped = 3,
    Modified = 4
}
```

| Value | Name                 | Description                  |
| ----: | -------------------- | ---------------------------- |
|   `1` | `Completed`          | Completed as planned         |
|   `2` | `PartiallyCompleted` | Only partially completed     |
|   `3` | `Skipped`            | Not performed                |
|   `4` | `Modified`           | Performed with modifications |

---

## PerformanceGrade

The swimming performance uses `PerformanceGrade` for technical evaluations such as technique, starts, turns, finishes, and pace consistency.

> The exact enum values are not included here because the provided implementation does not include the `PerformanceGrade` enum definition. Add its values to this section once the enum is finalized.

---

# Response Model

## SwimmingPerformanceResponse

Used for list and paginated responses.

```csharp
public class SwimmingPerformanceResponse
{
    public int Id { get; set; }

    public StrokeType Stroke { get; set; }

    public int DistanceMeters { get; set; }

    public int Repetitions { get; set; }

    public int RestIntervalSeconds { get; set; }

    public TimeSpan BestRepTime { get; set; }

    public TimeSpan AverageRepTime { get; set; }

    public TimeSpan WorstRepTime { get; set; }

    public PerformanceGrade Technique { get; set; }

    public PerformanceGrade Start { get; set; }

    public PerformanceGrade Turns { get; set; }

    public PerformanceGrade Finish { get; set; }

    public PerformanceGrade PaceConsistency { get; set; }

    public int? RPE { get; set; }

    public PerformanceStatus Status { get; set; }
}
```

---

# Request Models

## CreateSwimmingPerformanceRequest

```csharp
public class CreateSwimmingPerformanceRequest
{
    public int TrainingRecordId { get; set; }

    public List<SwimmingDrillRequest> SwimmingPerformances { get; set; } = [];
}
```

---

## SwimmingDrillRequest

```csharp
public class SwimmingDrillRequest
{
    public StrokeType Stroke { get; set; }

    public int DistanceMeters { get; set; }

    public int Repetitions { get; set; }

    public int RestIntervalSeconds { get; set; }

    public TimeSpan BestRepTime { get; set; }

    public TimeSpan AverageRepTime { get; set; }

    public TimeSpan WorstRepTime { get; set; }

    public PerformanceGrade Technique { get; set; }

    public PerformanceGrade Start { get; set; }

    public PerformanceGrade Turns { get; set; }

    public PerformanceGrade Finish { get; set; }

    public PerformanceGrade PaceConsistency { get; set; }

    public int? RPE { get; set; }

    public PerformanceStatus Status { get; set; }

    public string? CoachComment { get; set; }
}
```

---

## UpdateSwimmingPerformanceRequest

```csharp
public class UpdateSwimmingPerformanceRequest
{
    public StrokeType Stroke { get; set; }

    public int DistanceMeters { get; set; }

    public int Repetitions { get; set; }

    public int RestIntervalSeconds { get; set; }

    public TimeSpan BestRepTime { get; set; }

    public TimeSpan AverageRepTime { get; set; }

    public TimeSpan WorstRepTime { get; set; }

    public PerformanceGrade Technique { get; set; }

    public PerformanceGrade Start { get; set; }

    public PerformanceGrade Turns { get; set; }

    public PerformanceGrade Finish { get; set; }

    public PerformanceGrade PaceConsistency { get; set; }

    public int? RPE { get; set; }

    public PerformanceStatus Status { get; set; }

    public string? CoachComment { get; set; }
}
```

---

# Query Parameters

## SwimmingPerformanceQueryParameters

```csharp
public class SwimmingPerformanceQueryParameters : PaginationRequest
{
    public Guid? AthleteId { get; set; }

    public int? TrainingSessionId { get; set; }

    public StrokeType? Stroke { get; set; }

    public PerformanceStatus? Status { get; set; }

    public bool Descending { get; set; } = true;
}
```

### Example Queries

Get all:

```http
GET /api/Swimming-Performance
```

Filter by athlete:

```http
GET /api/Swimming-Performance?AthleteId=2d166dff-5101-4278-f6bc-08deefecdca4
```

Filter by stroke:

```http
GET /api/Swimming-Performance?Stroke=1
```

Filter by completed performances:

```http
GET /api/Swimming-Performance?Status=1
```

Combine filters:

```http
GET /api/Swimming-Performance?PageIndex=1&PageSize=20&AthleteId=2d166dff-5101-4278-f6bc-08deefecdca4&TrainingSessionId=2&Stroke=1&Status=1&Descending=true
```

---

# Swimming Performance Flow

The recommended flow is:

```text
Training Plan
      ↓
Training Session
      ↓
Athlete Attendance
      ↓
Training Record
      ↓
Swimming Performance
      ↓
Swimming Drill / Set Data
      ↓
Coach Review
      ↓
Athlete Performance History
```

A swimming performance is linked to a `TrainingRecord`.

The `TrainingRecord` represents the athlete's overall session performance, while `SwimmingPerformance` represents the swimming-specific details performed during that session.

### Example

```text
Training Record
│
├── Performance Rating: 9
├── Fatigue Level: 5
├── Session Completed: true
├── Injury Occurred: false
│
└── Swimming Performances
    │
    ├── 100m Freestyle × 4
    │   ├── Best: 1:08
    │   ├── Average: 1:10
    │   ├── Worst: 1:13
    │   ├── Technique: Good
    │   └── Turns: Good
    │
    └── 50m Kick × 8
        ├── Best: 0:42
        ├── Average: 0:44
        └── Pace Consistency: Good
```

---

# Important Notes

- `BestRepTime`, `AverageRepTime`, and `WorstRepTime` describe repetition/lap-level timing for the recorded swimming drill.
- `Turns`, `Start`, `Finish`, `Technique`, and `PaceConsistency` capture swimming-specific technical information that is not represented by the generic training record.
- `RPE` means **Rate of Perceived Exertion** and represents how difficult the effort was perceived to be.
- Multiple swimming performances can be created for one training record through `CreateSwimmingPerformanceRequest`.
- `GetByTrainingRecord` returns all swimming performances belonging to the selected training record.
- Swimming performances support filtering by athlete, training session, stroke, and status.
- Archive/restore is used instead of permanent deletion.
