# Real Time Communication (RTC)

> Real-time communication features including rooms and encrypted messaging.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [GET Requests](#get-requests)
  - [Rooms](#rooms)
  - [Room](#room)
  - [Room Messages](#room-messages)
- [PUT Requests](#put-requests)
  - [Add Room](#add-room)
  - [Add Room Messages](#add-room-messages)
- [DELETE Requests](#delete-requests)
  - [Delete Room](#delete-room)
  - [Delete Room Messages](#delete-room-messages)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `rtc` |

---

## GET Requests

### Rooms

Get a list of all rooms.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rooms` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=rtc&rooms=1
```

```http
GET /WorldClientAPI/rtc/rooms?session={session_id}
```

**Success Response**

```json
{
  "rooms": [
    {
      "id": string,
      "started": string,
      "ended": string,
      "members": string,
      "hasMessages": boolean
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Room ID |
| `started` | string | ISO-8601 date when room started |
| `ended` | string | ISO-8601 date when room ended |
| `members` | string | Comma-separated member email addresses |
| `hasMessages` | boolean | Whether room has messages |

**Error Response**

```json
{
  "error": string
}
```

---

### Room

Get a single room with its messages.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rooms` | number | Yes | Must be `1` |
| `id` | string | Yes | Room ID |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=rtc&rooms=1&id={room_id}
```

```http
GET /WorldClientAPI/rtc/rooms/{room_id}?session={session_id}
```

**Success Response**

```json
{
  "id": string,
  "started": string,
  "ended": string,
  "members": string,
  "hasMessages": boolean,
  "messages": [
    {
      "id": string,
      "salt": string,
      "data": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Room ID |
| `started` | string | ISO-8601 date when room started |
| `ended` | string | ISO-8601 date when room ended |
| `members` | string | Comma-separated member email addresses |
| `hasMessages` | boolean | Whether room has messages |
| `messages` | array | Array of message objects |
| `messages[].id` | string | Message ID |
| `messages[].salt` | string | Encryption salt |
| `messages[].data` | string | Encrypted message data |

**Error Response**

```json
{
  "error": string
}
```

---

### Room Messages

Get messages for a specific room.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messages` | number | Yes | Must be `1` |
| `room_id` | string | Yes | Room ID |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=rtc&messages=1&id={room_id}
```

```http
GET /WorldClientAPI/rtc/messages/{room_id}?session={session_id}
```

**Success Response**

```json
{
  "room_id": string,
  "messages": [
    {
      "id": string,
      "salt": string,
      "data": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `room_id` | string | Room ID |
| `messages` | array | Array of message objects |
| `messages[].id` | string | Message ID |
| `messages[].salt` | string | Encryption salt |
| `messages[].data` | string | Encrypted message data |

**Error Response**

```json
{
  "error": string
}
```

---

## PUT Requests

### Add Room

Create a new room.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rooms` | number | Yes | Must be `1` |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=rtc&rooms=1
```

```http
PUT /WorldClientAPI/rtc/rooms?session={session_id}
```

**Request Body**

```json
{
  "id": string,
  "started": string,
  "ended": string,
  "members": string,
  "messages": [
    {
      "id": string,
      "salt": string,
      "data": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Room ID |
| `started` | string | ISO-8601 date when room started |
| `ended` | string | ISO-8601 date when room ended |
| `members` | string | Comma-separated member email addresses |
| `messages` | array | Array of encrypted message objects |

**Success Response**

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": string
}
```

---

### Add Room Messages

Add messages to an existing room.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messages` | number | Yes | Must be `1` |
| `room_id` | string | Yes | Room ID |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=rtc&messages=1&id={room_id}
```

```http
PUT /WorldClientAPI/rtc/messages/{room_id}?session={session_id}
```

**Request Body**

```json
{
  "data": [
    {
      "id": string,
      "salt": string,
      "data": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `data` | array | Array of message objects to add |
| `data[].id` | string | Message ID |
| `data[].salt` | string | Encryption salt |
| `data[].data` | string | Encrypted message data |

**Success Response**

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": string
}
```

---

## DELETE Requests

### Delete Room

Delete a room.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rooms` | number | Yes | Must be `1` |
| `id` | string | Yes | Room ID |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=rtc&rooms=1&id={room_id}
```

```http
DELETE /WorldClientAPI/rtc/rooms/{room_id}?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": string
}
```

---

### Delete Room Messages

Delete all messages from a room.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messages` | number | Yes | Must be `1` |
| `room_id` | string | Yes | Room ID |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=rtc&messages=1&room_id={room_id}
```

```http
DELETE /WorldClientAPI/rtc/messages/{room_id}?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": string
}
```
