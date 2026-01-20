# Cloud Storage

> Integration with cloud storage providers: Dropbox, Google Drive, and OneDrive.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Overview](#overview)
- [Providers](#providers)
- [GET Requests](#get-requests)
  - [Initiate Access](#initiate-access)
  - [Check Access Status](#check-access-status)
  - [Revoke Access](#revoke-access)
  - [Refresh Token](#refresh-token)
  - [List Drives](#list-drives)
  - [List Folders](#list-folders)
  - [List Files](#list-files)
  - [Get File](#get-file)
- [POST Requests](#post-requests)
  - [Move Files](#move-files)
  - [Copy Files](#copy-files)
  - [Upload File](#upload-file)
- [PUT Requests](#put-requests)
  - [Rename File](#rename-file)
  - [Set Description](#set-description)
- [DELETE Requests](#delete-requests)
  - [Delete Files](#delete-files)

---

## Overview

The Cloud Storage API provides integration with three cloud storage providers:
- **Dropbox**
- **Google Drive**
- **OneDrive**

All three providers share a similar API structure, with minor differences noted in each section.

---

## Providers

| Provider | Route | Drives Support | Description Support |
|----------|-------|----------------|---------------------|
| Dropbox | `dropbox` | No | No |
| Google Drive | `googledrive` | Yes | Yes |
| OneDrive | `onedrive` | Yes | Yes |

**Base URLs:**

```http
/WorldClientAPI/dropbox/{action}
/WorldClientAPI/googledrive/{action}
/WorldClientAPI/onedrive/{action}
```

---

## GET Requests

### Initiate Access

Start the OAuth authentication flow with the cloud provider.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `access` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI/dropbox/access?session={session_id}
```

```http
GET /WorldClientAPI/googledrive/access?session={session_id}
```

```http
GET /WorldClientAPI/onedrive/access?session={session_id}
```

**Success Response**

Returns OAuth authorization URL or access status.

---

### Check Access Status

Check if the OAuth flow has completed.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `are_we_there_yet` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI/dropbox/are_we_there_yet?session={session_id}
```

**Success Response**

```json
{
  "connected": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| `connected` | boolean | Whether OAuth flow is complete |

---

### Revoke Access

Revoke the OAuth access token for the cloud provider.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `revoke` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI/dropbox/revoke?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

---

### Refresh Token

Refresh the OAuth access token.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `refresh` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI/googledrive/refresh?session={session_id}
```

**Success Response**

```json
{
  "success": true
}
```

---

### List Drives

List available drives. **Only available for Google Drive and OneDrive.**

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `drives` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI/googledrive/drives?session={session_id}
```

```http
GET /WorldClientAPI/onedrive/drives?session={session_id}
```

**Success Response**

```json
{
  "drives": [
    {
      "id": string,
      "name": string
    }
  ]
}
```

**Pagination**

To get more drives, add `/more`:

```http
GET /WorldClientAPI/googledrive/drives/more?session={session_id}
```

---

### List Folders

List folders in the cloud storage.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `folders` | number | Yes | Must be `1` |
| `folder` | string | No | Parent folder ID (OneDrive) |
| `drive` | string | No | Drive ID (Google Drive, OneDrive) |
| `search` | string | No | Search query |

**Example Request**

```http
GET /WorldClientAPI/dropbox/folders?session={session_id}
```

```http
GET /WorldClientAPI/googledrive/folders?session={session_id}&drive={drive_id}
```

```http
GET /WorldClientAPI/onedrive/folders?session={session_id}&drive={drive_id}&folder={folder_id}
```

**Success Response**

```json
{
  "folders": [
    {
      "id": string,
      "name": string,
      "path": string
    }
  ]
}
```

**Pagination**

To get more folders, add `/more`:

```http
GET /WorldClientAPI/dropbox/folders/more?session={session_id}
```

---

### List Files

List files in the cloud storage.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `files` | number | Yes | Must be `1` |
| `folder` | string | No | Folder ID |
| `drive` | string | No | Drive ID (Google Drive, OneDrive) |
| `search` | string | No | Search query |
| `q` | string | No | Query string (Google Drive) |

**Example Request**

```http
GET /WorldClientAPI/dropbox/files?session={session_id}&folder={folder_id}
```

```http
GET /WorldClientAPI/googledrive/files?session={session_id}&drive={drive_id}
```

**Success Response**

```json
{
  "files": [
    {
      "id": string,
      "name": string,
      "size": number,
      "modified": string,
      "mime_type": string
    }
  ]
}
```

**Pagination**

To get more files, add `/more`:

```http
GET /WorldClientAPI/googledrive/files/more?session={session_id}
```

---

### Get File

Get a specific file's metadata or download it. **Only available for Google Drive and OneDrive.**

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `file` | number | Yes | Must be `1` |
| `id` | string | Yes | File ID |

**Example Request**

```http
GET /WorldClientAPI/googledrive/file/{file_id}?session={session_id}
```

```http
GET /WorldClientAPI/onedrive/file/{file_id}?session={session_id}
```

---

## POST Requests

### Move Files

Move files to a different folder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | File ID(s), comma-separated for multiple |
| `move` | string | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI/dropbox/move?session={session_id}
Content-Type: application/json

{
  "id": "{file_id}",
  "move": "{destination_folder_id}"
}
```

**Success Response**

```json
{
  "success": true
}
```

---

### Copy Files

Copy files to a different folder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | File ID(s), comma-separated for multiple |
| `copy` | string | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI/googledrive/copy?session={session_id}
Content-Type: application/json

{
  "id": "{file_id}",
  "copy": "{destination_folder_id}"
}
```

**Success Response**

```json
{
  "success": true
}
```

---

### Upload File

Upload a file to cloud storage.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `upload` | number | Yes | Must be `1` |
| `parent` | string | No | Parent folder ID |

**Example Request**

```http
POST /WorldClientAPI/onedrive/upload?session={session_id}&parent={folder_id}
Content-Type: multipart/form-data
```

**Success Response**

```json
{
  "success": true,
  "id": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether upload succeeded |
| `id` | string | ID of the uploaded file |

---

## PUT Requests

### Rename File

Rename a file.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rename` | number | Yes | Must be `1` |
| `id` | string | Yes | File ID |
| `new_name` | string | Yes | New file name |

**Example Request**

```http
PUT /WorldClientAPI/dropbox/rename?session={session_id}
Content-Type: application/json

{
  "id": "{file_id}",
  "new_name": "new-filename.txt"
}
```

**Success Response**

```json
{
  "success": true
}
```

---

### Set Description

Set the description for a file. **Only available for Google Drive and OneDrive.**

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `description` | number | Yes | Must be `1` |
| `id` | string | Yes | File ID |
| `description` | string | Yes | New description text |

**Example Request**

```http
PUT /WorldClientAPI/googledrive/description?session={session_id}
Content-Type: application/json

{
  "id": "{file_id}",
  "description": "This is my file description"
}
```

**Success Response**

```json
{
  "success": true
}
```

---

## DELETE Requests

### Delete Files

Delete one or more files.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | File ID(s), comma-separated for multiple |

**Example Request**

```http
DELETE /WorldClientAPI/dropbox/file/{file_id}?session={session_id}
```

```http
DELETE /WorldClientAPI/googledrive/file/{file_id1},{file_id2}?session={session_id}
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
  "error": "InvalidParameter",
  "parameter": "id"
}
```
