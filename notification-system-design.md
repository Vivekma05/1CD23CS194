# Stage 1
GET /notifications — returns list of notifications 
GET /notifications/:id — returns a single notification
PUT /notifications/:id/read — marks a notification as read
GET /notifications/unread/count
json:
{
  "notifications": [
    {
      "id": "user_id",
      "type": "Placement",
      "message": "Google is hiring",
      "isRead": false,
      "timestamp": "2026-06-29T10:00:00"
    }
  ]
}
For realtime I would use WebSockets When a new notification is created on the backend
the server pushes it directly to the connected student's browser without needing them to refresh.