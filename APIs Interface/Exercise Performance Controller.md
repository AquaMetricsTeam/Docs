# 1. Get Swimming Performances By Training Record

### Request

```http
GET /api/Exercise-Performance/training-record/{trainingRecordId}
```

### Example

```http
GET /api/Exercise-Performance/training-record/2
```

### Description

Returns all Exercise performance entries associated with a specific training record.

This endpoint is useful when displaying the Exercise details of a completed training session.

### Response

```text
ApiResponse<List<ExercisePerformanceResponse>>
```
