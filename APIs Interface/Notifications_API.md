## 1. Get My Notifications

### Request

GET /api/Notifications

#### Headers Authorization: Bearer <access-token>

### Resposne

```json
{
  "data": [
    {
      "id": 1,
      "type": 0,
      "title": "New Training Session",
      "message": "A new training session has been assigned to you.",
      "isRead": false,
      "createdAt": "2026-08-18T15:30:00Z"
    }
  ]
}
```

## Response Model

| Property    | Type               | Description                                      |
| ----------- | ------------------ | ------------------------------------------------ |
| `Id`        | `int`              | Unique notification identifier                   |
| `Type`      | `NotificationType` | Type/category of the notification                |
| `Title`     | `string`           | Notification title                               |
| `Message`   | `string`           | Notification content                             |
| `IsRead`    | `bool`             | Indicates whether the notification has been read |
| `CreatedAt` | `DateTime`         | Date and time when the notification was created  |

## 2. Get Unread Notifications Count

### Request

GET /api/Notifications/unread-count

### Resopnse

```json
{
  "data": 5
}
```

## 3. Mark Notification as Read

### Requests

PUT /api/Notifications/{id}/read

#### Parameters

| Parameter | Type  | Location | Description                            |
| --------- | ----- | -------- | -------------------------------------- |
| `id`      | `int` | Route    | ID of the notification to mark as read |

#### Example

PUT /api/Notifications/15/read

### Response

204 No Content

## 4. Mark All Notifications as Read

### Request

PUT /api/Notifications/read-all

### Response

Response

public enum NotificationType
{
AccountCreated = 1,

    TrainingAssigned,

    NutritionAssigned, // Done

    AssessmentRecorded,

    AttendanceRecorded, //Done

    AIRecommendationReady,

    SystemAnnouncement,

    GroupAssigned,

    TrainingSessionAssigned, // Done

    CoachAssigned, // Done

    CoachRemoved // Done

}
