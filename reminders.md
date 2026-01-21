# Reminders

> Task and calendar reminder management for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [Reminder Object](#reminder-object)
- [GET Requests](#get-requests)
  - [Get Reminders](#get-reminders)
- [PUT Requests](#put-requests)
  - [Mark Seen](#mark-seen)
  - [Mark All Seen](#mark-all-seen)
  - [Mark Unseen](#mark-unseen)
- [DELETE Requests](#delete-requests)
  - [Clear Reminder](#clear-reminder)
  - [Clear All Reminders](#clear-all-reminders)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `reminders` |

---

## Reminder Object

This route retrieves Task and Calendar reminders within 24 hours and within the past two weeks that have been set by the user.

**Important Notes:**
- Reminders older than two weeks are automatically removed by the server.
- Due to multiple reminders in calendar events, you may have up to 6 reminders for the same event.
- Each reminder can be marked seen/cleared independently.
- If a user clears an event reminder, additional reminders for the same event will still appear.

**Reminder Object Structure**

```json
{
  "type": string,
  "subject": string,
  "folderId": number,
  "id": number,
  "uuid": string,
  "reminder_iso8601": string,
  "start": {
    "year": number,
    "month": number,
    "day": number,
    "hour": number,
    "minute": number
  },
  "end": {
    "year": number,
    "month": number,
    "day": number,
    "hour": number,
    "minute": number
  },
  "due": {
    "year": number,
    "month": number,
    "day": number
  },
  "reminder": {
    "year": number,
    "month": number,
    "day": number,
    "hour": number,
    "minute": number
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Either `"event"` or `"task"` |
| `subject` | string | Reminder subject |
| `folderId` | number | Folder ID |
| `id` | number | Item ID |
| `uuid` | string | Unique identifier |
| `reminder_iso8601` | string | ISO-8601 date (YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `start` | object | Start date/time (for events) |
| `end` | object | End date/time (for events only) |
| `due` | object | Due date (for tasks only) |
| `reminder` | object | Reminder date/time |

---

## GET Requests

### Get Reminders

Retrieve all active reminders.

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=reminders
```

```http
GET /WorldClientAPI/reminders?session={session_id}
```

**Success Response**

```json
{
  "reminders": []
}
```

| Field | Type | Description |
|-------|------|-------------|
| `reminders` | array | Array of reminder objects |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

## PUT Requests

### Mark Seen

Mark a specific reminder as seen.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `seen` | number | Yes | Must be `1` |
| `id` | string | Yes | Reminder UUID |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=reminders&seen=1&id={reminder_id}
```

```http
PUT /WorldClientAPI/reminders/seen/{reminder_id}?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

### Mark All Seen

Mark all reminders as seen.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `seen` | number | Yes | Must be `1` |
| `all` | number | Yes | Must be `1` |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=reminders&seen=1&all=1
```

```http
PUT /WorldClientAPI/reminders/seen/all?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

### Mark Unseen

Mark a specific reminder as unseen.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `unseen` | number | Yes | Must be `1` |
| `id` | string | Yes | Reminder UUID |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=reminders&unseen=1&id={reminder_id}
```

```http
PUT /WorldClientAPI/reminders/unseen/{reminder_id}?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

## DELETE Requests

### Clear Reminder

Clear a specific reminder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `clear` | number | Yes | Must be `1` |
| `id` | string | Yes | Reminder UUID |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=reminders&clear=1&id={reminder_id}
```

```http
DELETE /WorldClientAPI/reminders/clear/{reminder_id}?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

### Clear All Reminders

Clear all reminders.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `clear` | number | Yes | Must be `1` |
| `all` | number | Yes | Must be `1` |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=reminders&clear=1&all=1
```

```http
DELETE /WorldClientAPI/reminders/clear/all?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |
