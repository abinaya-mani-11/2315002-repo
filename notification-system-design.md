# Notification System Design

# Overview

This md file describes the REST API contract for a notification system that allows users to receive notifications after logging in.

The API supports:

- Get all notifications
- Get unread notifications
- Mark notification as read
- Mark all notifications as read
- Delete a notification

# Stage 1

# 1. Get All Notifications

### Response 

```json
{
  "success": true,
  "count": 2,
  "notifications": [
    {
      "id": "not_001",
      "title": "Order Confirmed",
      "message": "Your order has been confirmed.",
      "type": "order",
      "isRead": false,
    },
    {
      "id": "not_002",
      "title": "Welcome",
      "message": "Welcome to our application.",
      "type": "system",
      "isRead": true
    }
  ]
}
```

---

# 2. Get Unread Notifications

### Response

```json
{
  "success": true,
  "count": 1,
  "notifications": [
    {
      "id": "not_001",
      "title": "Event Date",
      "message": "Date of the Event has been received.",
      "type": "Event",
      "isRead": false
    }
  ]
}
```

---

# 3. Mark Notification as Read

### Response

```json
{
  "success": true,
  "message": "Notification marked as read."
}
```

---

# 4. Mark All Notifications as Read

### Response

```json
{
  "success": true,
  "message": "All notifications marked as read."
}
```

---

# 5. Delete Notification

### Response

```json
{
  "success": true,
  "message": "Notification deleted successfully."
}
```

---

# Error Response

```json
{
  "success": false,
  "message": "Notification not found."
}
```
---

### Server Push Message

```json
{
  "event": "NEW_NOTIFICATION",
  "data": {
    "id": "not_003",
    "title": "Payment Successful",
    "message": "Your payment has been received.",
    "type": "payment",
    "isRead": false
  }
}
```


# Stage 2

To save the data reliably, the persistant storage i would suggest is PostgreSQL

- PostgreSQL is easily managable, easy to collaborate and can handle complex data with high reliability

- i would suggest storing the data as key-value pair

- if data volume increases, it would require optimizing queries, new scaling statergies.

- according to rest API, sending new notification about the mail received to the logging user.


---

# Stage 3 

## Existing Query

```sql
SELECT *
FROM notifications
WHERE studentID = 1042
  AND isRead = false
ORDER BY createdAt ASC;
```

---

## Is this query accurate?

Yes.

The query correctly returns all unread notifications for student `1042` ordered by their creation time.

---

## Why is it slow?

With approximately:

- 50,000 students
- 5,000,000 notifications

this query becomes slow if proper indexes are not available.

Reasons include:

1. Full Table Scan
   - Without an index, the database checks all 5 million rows to find matching records.
   - Time Complexity: O(N)

2. Sorting Cost
   - `ORDER BY createdAt` requires sorting the matching rows.
   - Without an index, sorting increases execution time.

---

## Improved Query

Retrieve only the required columns.

```sql
SELECT
    id,
    title,
    message,
    notificationType,
    createdAt
FROM notifications
WHERE studentID = 1042
  AND isRead = false
ORDER BY createdAt ASC;
```

---

## Recommended Index

Create a composite index.

```sql
CREATE INDEX idx_notifications_student_read_created
ON notifications(studentID, isRead, createdAt);
```


## Should indexes be added on every column?

No.

Adding indexes to every column is not a good practice.

## Query: Students Who Received Placement Notifications in the Last 7 Days

```sql
SELECT DISTINCT studentID
FROM notifications
WHERE notificationType = 'Placement'
  AND createdAt >= NOW() - INTERVAL '7 days';
```

---


# Stage 4

## Problem

The application fetches notifications from the database every time a student loads a page. As the system has grown to 50,000 students and 5,000,000 notifications, this results in:

- High number of database queries
- Increased database load
- Slower page loading

---

## Recommended Solutions

### 1. Pagination 

Instead of loading all notifications, fetch them in small batches.

Example:

```http
GET /notifications?page=1&limit=20
```

Only the first 20 notifications are returned. Additional notifications can be loaded as needed.

Advantages
- Faster response time
- Lower memory usage
- Reduced database load

Tradeoff
- Multiple API requests are required to view older notifications.


### 2. Fetch Only Required Columns

Avoid:

```sql
SELECT *
FROM notifications;
```

Instead:

```sql
SELECT id,
       title,
       message,
       isRead,
       createdAt
FROM notifications;
```

Advantages
- Faster queries

---






