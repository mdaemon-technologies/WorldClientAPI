# Ping

> Keep-alive endpoint for session maintenance.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Overview

The Ping endpoint provides a simple keep-alive mechanism to maintain session activity. It can be used by clients to prevent session timeouts during periods of inactivity.

---

## GET Request

### Ping

Send a ping request and receive a pong response.

**Example Request**

```http
GET /WorldClientAPI/ping?session={session_id}
```

**Success Response**

```json
{
  "pong": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `pong` | boolean | Always `true` on success |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid or expired session |

---

## Usage Notes

- Each successful ping request updates the session's last access time
- Use this endpoint sparingly to avoid unnecessary server load
- The session timeout period is configured server-side in `WorldClient.ini`
- To disable keep-alive behavior, send the header `X-No-Keep-Alive: true` (if `HonorNoKeepAlive` is enabled for the domain)
