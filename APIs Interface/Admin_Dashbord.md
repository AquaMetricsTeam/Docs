# Admin Dashboard API Documentation

## Base Route

## Authorization

**Required Role:** `Admin`  
All endpoints require authentication via Bearer token and the user must have the `Admin` role.

---

## Endpoints

### 1. Get Admin Dashboard

**GET** `/api/admin-dashboard`

Retrieves the complete admin dashboard data including KPIs, performance trends, fatigue trends, injuries over time, performance vs fatigue correlation, and athletes distribution per domain.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

#### Response

**Status:** `200 OK`

**Body:** `ApiResponse<AdminDashboardResponse>`

```json
{
  "success": true,
  "message": null,
  "data": {
    "totalAthletes": 150,
    "totalSessions": 320,
    "totalInjuries": 12,
    "performanceTrend": [
      {
        "date": "2026-07-01",
        "value": 85
      },
      {
        "date": "2026-07-08",
        "value": 88
      },
      {
        "date": "2026-07-15",
        "value": 82
      }
    ],
    "fatigueTrend": [
      {
        "date": "2026-07-01",
        "value": 3
      },
      {
        "date": "2026-07-08",
        "value": 4
      },
      {
        "date": "2026-07-15",
        "value": 2
      }
    ],
    "injuriesOverTime": [
      {
        "date": "2026-07-01",
        "value": 1
      },
      {
        "date": "2026-07-08",
        "value": 0
      },
      {
        "date": "2026-07-15",
        "value": 2
      }
    ],
    "performanceVsFatigue": [
      {
        "trainingRecordId": 101,
        "trainingSessionId": 55,
        "sessionDate": "2026-07-10",
        "performanceRating": 90,
        "fatigueLevel": 2
      },
      {
        "trainingRecordId": 102,
        "trainingSessionId": 56,
        "sessionDate": "2026-07-12",
        "performanceRating": 75,
        "fatigueLevel": 5
      }
    ],
    "athletesPerDomain": [
      {
        "domainId": 1,
        "domainName": "Swimming",
        "athleteCount": 80
      },
      {
        "domainId": 2,
        "domainName": "Fitness",
        "athleteCount": 70
      }
    ]
  }
}
```
