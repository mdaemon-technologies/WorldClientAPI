# Conversations

> Chat conversation history storage and retrieval for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Overview](#overview)
- [Required Parameters](#required-parameters)
- [GET Requests](#get-requests)
  - [Get All Conversations](#get-all-conversations)
  - [Get Single Conversation](#get-single-conversation)
- [PUT Requests](#put-requests)
  - [Add Message](#add-message)

---

## Overview

The Conversations API provides storage and retrieval of chat message history. This allows chat clients to persist conversation data on the server for synchronization across devices.

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `conversations` |

---

## GET Requests

### Get All Conversations

Retrieve all stored conversations for the authenticated user.

**Example Request**

```http
GET /WorldClientAPI/conversations?session={session_id}
```

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `before` | string | No | ISO-8601 timestamp; filters messages to only include those with timestamps on or before this time |
| `limit` | integer | No | Maximum number of messages to return per conversation |

**Behavior**

- When `before` is specified, each conversation's messages array is filtered to only include messages with timestamps on or before the specified time
- When `limit` is specified, each conversation's messages array is limited to the most recent N messages

**Example: Get Conversations with Message Filtering**

```http
GET /WorldClientAPI/conversations?session={session_id}&before=2025-01-20T10:00:00-05:00
```

**Example: Get Conversations with Limited Messages Per Conversation**

```http
GET /WorldClientAPI/conversations?session={session_id}&limit=50
```

**Success Response**

```json
{
  "conversations": [
    {
      "jid": string,
      "type": string,
      "lastMessageTime": string,
      "messages": [
        {
          "id": string,
          "from": string,
          "to": string,
          "body": string,
          "timestamp": string,
          "type": string,
          "direction": string
        }
      ]
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `conversations` | array | Array of conversation objects |
| `conversations[].jid` | string | Jabber ID (contact identifier) |
| `conversations[].type` | string | Conversation type: `chat` or `groupchat` |
| `conversations[].lastMessageTime` | string | ISO-8601 timestamp of last message |
| `conversations[].messages` | array | Array of message objects |

**Message Object**

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique message identifier |
| `from` | string | Sender JID |
| `to` | string | Recipient JID |
| `body` | string | Message content |
| `timestamp` | string | ISO-8601 timestamp |
| `type` | string | Message type: `chat` or `groupchat` |
| `direction` | string | Message direction: `incoming` or `outgoing` |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidParameter` | Invalid ISO-8601 format for `before` parameter |
| 401 | `Unauthorized` | Invalid session |
| 403 | `InadequatePermissions` | User access denied |

---

### Get Single Conversation

Retrieve a specific conversation by JID with optional message limiting and pagination.

**Example Request**

```http
GET /WorldClientAPI/conversations?session={session_id}&jid={jid}
```

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jid` | string | Yes | Jabber ID of the conversation to retrieve |
| `limit` | integer | No | Maximum number of messages to return |
| `before` | string | No | ISO-8601 timestamp; returns messages before this time 

**Behavior**

- When `jid` is provided, returns a single conversation object instead of the full conversations array
- When `limit` is specified without `before`, returns the most recent N messages
- When both `limit` and `before` are specified, returns up to N messages with timestamps before the specified time
- When `before` is specified without `limit`, returns only messages with timestamps
before or at the specified time

**Example: Get Conversation with Limited Messages**

```http
GET /WorldClientAPI/conversations?session={session_id}&jid=contact@example.com&limit=50
```

**Example: Get Older Messages (Pagination)**

```http
GET /WorldClientAPI/conversations?session={session_id}&jid=contact@example.com&limit=50&before=2025-01-20T10:00:00-05:00
```

**Success Response**

```json
{
  "conversation": {
    "jid": string,
    "type": string,
    "lastMessageTime": string,
    "messages": [
      {
        "id": string,
        "from": string,
        "to": string,
        "body": string,
        "timestamp": string,
        "type": string,
        "direction": string
      }
    ]
  }
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `MissingParameter` | `jid` parameter is empty |
| 400 | `InvalidParameter` | No conversation found with the specified JID |
| 400 | `InvalidParameter` | Invalid ISO-8601 format for `before` parameter |
| 401 | `Unauthorized` | Invalid session |
| 403 | `InadequatePermissions` | User access denied |

---

## PUT Requests

### Add Message

Add a new message to a conversation. If the conversation doesn't exist, it will be created automatically.

**Example Request**

```http
PUT /WorldClientAPI/conversations?session={session_id}
Content-Type: application/json
```

**Request Body**

```json
{
  "message": {
    "id": string,
    "from": string,
    "to": string,
    "body": string,
    "timestamp": string,
    "type": string,
    "direction": string
  }
}
```

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `message` | object | Yes | Message object to store |
| `message.id` | string | Yes | Unique message identifier |
| `message.from` | string | Yes | Sender JID |
| `message.to` | string | Yes | Recipient JID |
| `message.body` | string | Yes | Message content (can be empty) |
| `message.timestamp` | string | Yes | ISO-8601 timestamp |
| `message.type` | string | Yes | Must be `chat` or `groupchat` |
| `message.direction` | string | Yes | Must be `incoming` or `outgoing` |

**Type Values**

| Value | Description |
|-------|-------------|
| `chat` | One-on-one conversation |
| `groupchat` | Group conversation |

**Direction Values**

| Value | Description |
|-------|-------------|
| `incoming` | Message received from another user |
| `outgoing` | Message sent by the authenticated user |

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `MissingParameter` | Required field missing from message object |
| 400 | `InvalidParameter` | Invalid value for `type` or `direction` |
| 401 | `Unauthorized` | Invalid session |
| 500 | `InternalError` | Failed to save conversation |

**Example: Adding an Outgoing Message**

```http
PUT /WorldClientAPI/conversations?session={session_id}
Content-Type: application/json

{
  "message": {
    "id": "msg-12345",
    "from": "user@example.com",
    "to": "contact@example.com",
    "body": "Hello, how are you?",
    "timestamp": "2025-01-20T10:30:00-05:00",
    "type": "chat",
    "direction": "outgoing"
  }
}
```

**Example: Adding an Incoming Message**

```http
PUT /WorldClientAPI/conversations?session={session_id}
Content-Type: application/json

{
  "message": {
    "id": "msg-12346",
    "from": "contact@example.com",
    "to": "user@example.com",
    "body": "I'm doing well, thanks!",
    "timestamp": "2025-01-20T10:31:00-05:00",
    "type": "chat",
    "direction": "incoming"
  }
}
```

---

## Notes

- Conversations are automatically grouped by JID (contact identifier)
- For incoming messages, the conversation is keyed by the `from` field
- For outgoing messages, the conversation is keyed by the `to` field
- The `lastMessageTime` field is automatically updated when a new message is added
