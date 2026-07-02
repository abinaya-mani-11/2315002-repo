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




