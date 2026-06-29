# Stage 1
GET /notifications — returns list of notifications 
GET /notifications/:id — returns a single notification
PUT /notifications/:id/read — marks a notification as read
GET /notifications/unread/count
json:
{
  "notifications": [
    {
      "id": "uuid",
      "type": "Placement",
      "message": "Google is hiring",
      "isRead": false,
      "timestamp": "2026-06-29T10:00:00"
    }
  ]
}
For realtime I would use WebSockets When a new notification is created on the backend
the server pushes it directly to the connected student's browser without needing them to refresh.


#stage 2

CREATE TABLE notifications (
  id UUID PRIMARY KEY,
  student_id INT NOT NULL,
  type VARCHAR(20) CHECK (type IN ('Event', 'Result', 'Placement')),
  message TEXT,
  is_read BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW()
);

As the data grows to millions of records, the biggest challenges will be slower query performance and increased storage usage.
To handle this, I would create indexes on student_id and created_at to speed up searches, implement pagination in the API so only a limited number of records are fetched at a time, and move notifications older than six months to an archive table to keep the main table efficient.


#Stage 3

Is the query accurate?
Yes it is correct. It fetches unread notifications for student 1042 ordered by time.

Why is it slow?
There is no index on studentID or isRead. So the database reads all 5 million rows one by one to find the matching ones. That is why it is slow.

Fix:
CREATE INDEX idx_student_unread ON notifications(studentID, isRead, createdAt);

Now it directly jumps to the matching rows instead of scanning everything.

Is adding indexes on every column a good idea?
No.Indexes slow down inserts and updates because every time a new row is added all indexes have to be updated too. Only add indexes on columns you actually filter by.

Placement notifications in last 7 days:
SELECT DISTINCT studentID
FROM notifications
WHERE notificationType='Placement'
AND createdAt>= NOW()-INTERVAL'7 days';
