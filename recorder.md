# Recorder

> Audio recording upload and management for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [GET Requests](#get-requests)
  - [Download Track](#download-track)
- [POST Requests](#post-requests)
  - [Upload MP3](#upload-mp3)
  - [Save To Documents](#save-to-documents)
  - [Quick Send](#quick-send)
  - [Rename Track](#rename-track)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `recorder` |

---

## GET Requests

### Download Track

Download an MP3 track.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `filename` | string | Yes | Track filename |
| `download` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=recorder&filename={filename}&download=1
```

```http
GET /WorldClientAPI/recorder/download/{filename}?session={session_id}
```

**Success Response**

Download begins.

**Error Responses**

| Status | Description |
|--------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

---

## POST Requests

### Upload MP3

Upload a recorded MP3 file in chunks.

**Upload Specifications:**
- Maximum chunk size: 2MB
- Maximum chunks: 100
- Maximum total file size: 200MB
- Uploads must be in order (Part 1 before Part 2, etc.)

> **Warning:** If you send Part 2 before Part 1, the file will upload but will be corrupted.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `filename` | string | Yes | Track filename |
| `parts` | number | Yes | Total number of parts |
| `part` | number | Yes | Current part number |
| `upload` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=recorder&filename={filename}&upload=1&parts={parts}&part={part}
```

```http
POST /WorldClientAPI/recorder/upload/{filename}/{parts}/{part}?session={session_id}
```

**Success Response**

```json
{
  "success": true,
  "filename": string,
  "bytesread": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether upload succeeded |
| `filename` | string | Uploaded filename |
| `bytesread` | number | Number of bytes read |

**Error Response**

```json
{
  "error": "File upload failed."
}
```

---

### Save To Documents

Save a recording to the default documents folder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `filename` | string | Yes | Track filename |
| `save` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=recorder&filename={filename}&save=1
```

```http
POST /WorldClientAPI/recorder/save/{filename}?session={session_id}
```

**Success Response**

```json
{
  "success": true,
  "folderId": number,
  "filename": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether save succeeded |
| `folderId` | number | Document folder ID |
| `filename` | string | Saved filename |

**Error Responses**

```json
{
  "error": "This type of file is blocked."
}
```

```json
{
  "error": "Could not save the file to your default documents folder."
}
```

```json
{
  "error": "Your request was invalid."
}
```

```json
{
  "error": "File size exceeds the maximum file size of %d k."
}
```

---

### Quick Send

Send a recording via email without going to compose view. Default compose settings will be applied.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `filename` | string | Yes | Track filename |
| `send` | number | Yes | Must be `1` |
| `to` | string | Yes | Recipient email address(es) |
| `cc` | string | No | CC recipients |
| `bcc` | string | No | BCC recipients |
| `subject` | string | No | Email subject (auto-generated if empty) |
| `body` | string | No | Email body (auto-generated if empty) |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=recorder&filename={filename}&send=1&to={to}
```

**Success Response**

```json
{
  "success": true
}
```

**Error Responses**

```json
{
  "error": "There was a bad email address included in the To, CC, or BCC field."
}
```

```json
{
  "error": "Error sending message!"
}
```

---

### Rename Track

Rename an existing recording.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `filename` | string | Yes | Current track filename |
| `rename` | number | Yes | Must be `1` |
| `newname` | string | Yes | New ASCII filename |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=recorder&filename={filename}&rename=1&newname={newname}
```

```http
POST /WorldClientAPI/recorder/rename/{filename}?session={session_id}
```

**Request Body**

```json
{
  "newname": string
}
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
  "error": "Could not rename the item."
}
```
