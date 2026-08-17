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
