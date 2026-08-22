## 1.SingalR

### Hub URL

/hubs/notifications

[Authorize]

### Authentication

الـ Frontend يرسل الـ JWT باستخدام accessTokenFactory.

Example:

```javascript
const connection = new HubConnectionBuilder()
  .withUrl("/hubs/notifications", {
    accessTokenFactory: () => accessToken,
  })
  .withAutomaticReconnect()
  .build();
```

## 2.SignalR Client Event

```text
ReceiveNotification
```

Example

```javascript
connection.on("ReceiveNotification", (notification) => {
  console.log("New notification:", notification);
});
```

## 3.SignalR Payload

الـ Payload اللي بييجي من SignalR هو نفس NotificationResponse اللي بيرجع من REST API.

```json
{
  "id": 123,
  "type": 10,
  "title": "New Training Session",
  "message": "A new training session has been assigned to you.",
  "isRead": false,
  "createdAt": "2026-08-22T10:30:00Z"
}
```

### 4.SignalR Connection flow

Login
↓
Receive JWT Access Token
↓
Create SignalR Connection
↓
Connect to /hubs/notifications
↓
Listen for ReceiveNotification
↓
Receive NotificationResponse
↓
Update Notifications UI
↓
Update Unread Badge

يفضل استخدام
.withAutomaticReconnect()

## 5.REST API

```text
/api/Notifications

Authorization: Bearer <access-token>
```

### 5.1 Get My Notifications

```text
GET /api/Notifications?page=1&pageSize=20
```

| Parameter  | Type | Required | Description                      |
| ---------- | ---- | -------- | -------------------------------- |
| `page`     | int  | No       | Page number, starts from 1       |
| `pageSize` | int  | No       | Number of notifications per page |

### 5.1 Get UnRead Count

GET /api/Notifications/unread-count

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": 5
}
```

### 5.3 Mark Notification as Read

PUT /api/Notifications/{id}/read

### 5.4 Mark All Notifications as Read

PUT /api/Notifications/read-all

## 6. Notification Type Enum

```c#
public enum NotificationType
{
    AccountCreated = 1,
    TrainingAssigned = 2,
    NutritionAssigned = 3,
    AssessmentRecorded = 4,
    AttendanceRecorded = 5,
    AIRecommendationReady = 6,
    SystemAnnouncement = 7,
    GroupAssigned = 8,
    InjuryOccured = 9,
    TrainingSessionAssigned = 10,
    CoachAssigned = 11,
    CoachRemoved = 12
}
```

| Value | Type                      | Meaning                       |
| ----: | ------------------------- | ----------------------------- |
|   `1` | `AccountCreated`          | Account was created           |
|   `2` | `TrainingAssigned`        | Training plan was assigned    |
|   `3` | `NutritionAssigned`       | Nutrition plan was assigned   |
|   `4` | `AssessmentRecorded`      | Assessment was recorded       |
|   `5` | `AttendanceRecorded`      | Attendance was recorded       |
|   `6` | `AIRecommendationReady`   | AI recommendation is ready    |
|   `7` | `SystemAnnouncement`      | System announcement           |
|   `8` | `GroupAssigned`           | Group assignment occurred     |
|   `9` | `InjuryOccured`           | Injury was recorded           |
|  `10` | `TrainingSessionAssigned` | Training session was assigned |
|  `11` | `CoachAssigned`           | Coach was assigned            |
|  `12` | `CoachRemoved`            | Coach assignment was removed  |
