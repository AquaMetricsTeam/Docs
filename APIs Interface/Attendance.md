# Attendance API Documentation

## Base Route

### api/attendance

## Authorization

**Required Role:** `Coach`  
All endpoints require authentication via Bearer token and the user must have the `Coach` role.

---

## Endpoints

### 1. Mark Attendance

**POST** `/api/attendance`

Marks attendance for athletes in a training session.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

**Body:** `MarkAttendanceRequest`
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `trainingSessionId` | `int` | ✅ Yes | Training session ID |
| `attendance` | `List<AttendanceItemRequest>` | ✅ Yes | List of attendance records |

**AttendanceItemRequest:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `athleteId` | `Guid` | ✅ Yes | Athlete ID |
| `status` | `AttendanceStatus` | ✅ Yes | Attendance status (`Present`, `Absent`, `Late`, `Excused`) |

#### Request Example

```json
{
  "trainingSessionId": 1,
  "attendance": [
    {
      "athleteId": "550e8400-e29b-41d4-a716-446655440000",
      "status": 1
    },
    {
      "athleteId": "660e8400-e29b-41d4-a716-446655440001",
      "status": 2
    }
  ]
}
```

##### Response

```json
{
  "success": true,
  "message": null,
  "data": true
}
```

### 2. Get Attendance by Session

GET /api/attendance/session/{trainingSessionId}
Retrieves all attendance records for a specific training session.

#### Request

Headers:
Authorization: Bearer token (required)

##### Route Parameters:

| Field               | Type  | Description         |
| ------------------- | ----- | ------------------- |
| `trainingSessionId` | `int` | Training session ID |

#### Response

```json
{
  "success": true,
  "message": null,
  "data": [
    {
      "id": 1,
      "athleteId": "550e8400-e29b-41d4-a716-446655440000",
      "athleteName": "John Doe",
      "status": 1,
      "recordedAt": "2026-08-05T09:15:00Z",
      "recordedById": "770e8400-e29b-41d4-a716-446655440002",
      "recordedByName": "Coach Ahmed"
    },
    {
      "id": 2,
      "athleteId": "660e8400-e29b-41d4-a716-446655440001",
      "athleteName": "Jane Smith",
      "status": 2,
      "recordedAt": "2026-08-05T09:15:00Z",
      "recordedById": "770e8400-e29b-41d4-a716-446655440002",
      "recordedByName": "Coach Ahmed"
    }
  ]
}
```

### 3. Get Attendance by Athlete

GET /api/attendance/athlete/{athleteId}
Retrieves paginated attendance history for a specific athlete.

##### Request

| Field      | Type  | Required | Default | Description                                       |
| ---------- | ----- | -------- | ------- | ------------------------------------------------- |
| `page`     | `int` | ❌ No    | `1`     | Page number (inherited from PaginationRequest)    |
| `pageSize` | `int` | ❌ No    | `20`    | Items per page (inherited from PaginationRequest) |

#### Response

```json
{
  "success": true,
  "message": null,
  "data": {
    "items": [
      {
        "id": 1,
        "athleteId": "550e8400-e29b-41d4-a716-446655440000",
        "athleteName": "John Doe",
        "status": 1,
        "recordedAt": "2026-08-05T09:15:00Z",
        "recordedById": "770e8400-e29b-41d4-a716-446655440002",
        "recordedByName": "Coach Ahmed"
      }
    ],
    "page": 1,
    "pageSize": 20,
    "totalCount": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

#### 4. Delete Attendance Record

#### DELETE /api/attendance/{id}

Deletes a specific attendance record.

#### Response

```json
{
  "success": true,
  "message": null,
  "data": true
}
```
