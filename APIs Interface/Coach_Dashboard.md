# Coach Dashboard API Documentation

## Base Route

## Authorization

**Required Role:** `Coach`  
All endpoints require authentication via Bearer token and the user must have the `Coach` role.

---

## Endpoints

### 1. Get Coach Dashboard

**GET** `/api/coach-dashboard`

Retrieves the complete coach dashboard data including KPIs for assigned athletes, total sessions, injuries, and various performance trends.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

#### Response

**Status:** `200 OK`

**Body:** `ApiResponse<CoachDashboardResponse>`

```json
{
  "success": true,
  "message": null,
  "data": {
    "assignedAthletes": 25,
    "totalSessions": 120,
    "injuries": 3,

    "injuredAthletes": [
      {
        "athleteId": "7f8a1c2d-3e4b-4a5c-9d6e-123456789abc",
        "fullName": "Ahmed Mohamed",
        "profilePictureUrl": "https://example.com/profile.jpg",
        "injuryType": 1,
        "injuryBodyPart": 2,
        "injuryComment": "Shoulder pain during freestyle",
        "injuryDate": "2026-08-12T10:30:00"
      },
      {
        "athleteId": "8a9b2c3d-4e5f-4a6b-9c7d-234567890abc",
        "fullName": "Omar Ali",
        "profilePictureUrl": null,
        "injuryType": 2,
        "injuryBodyPart": 1,
        "injuryComment": "Knee pain after training",
        "injuryDate": "2026-08-11T16:20:00"
      }
    ],

    "performanceTrend": [
      {
        "date": "2026-08-10",
        "value": 85
      },
      {
        "date": "2026-08-11",
        "value": 88
      },
      {
        "date": "2026-08-12",
        "value": 82
      }
    ],

    "fatigueTrend": [
      {
        "date": "2026-08-10",
        "value": 3
      },
      {
        "date": "2026-08-11",
        "value": 4
      },
      {
        "date": "2026-08-12",
        "value": 2
      }
    ],

    "injuriesOverTime": [
      {
        "date": "2026-08-10",
        "value": 0
      },
      {
        "date": "2026-08-11",
        "value": 1
      },
      {
        "date": "2026-08-12",
        "value": 0
      }
    ],

    "performanceVsFatigue": [
      {
        "trainingRecordId": 201,
        "trainingSessionId": 45,
        "sessionDate": "2026-08-10",
        "performanceRating": 90,
        "fatigueLevel": 2
      },
      {
        "trainingRecordId": 202,
        "trainingSessionId": 46,
        "sessionDate": "2026-08-11",
        "performanceRating": 75,
        "fatigueLevel": 5
      }
    ]
  }
}
```
