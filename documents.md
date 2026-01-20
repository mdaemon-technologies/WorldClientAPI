# Documents

> Document storage and file management for MDaemon Webmail.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [GET Requests](#get-requests)
  - [Single Document](#single-document)
  - [All Documents](#all-documents)
  - [Sort Documents](#sort-documents)
  - [Search Documents](#search-documents)
  - [Download Document](#download-document)
- [POST Requests](#post-requests)
  - [Move](#move)
  - [Copy](#copy)
  - [Upload Document](#upload-document)
- [PUT Requests](#put-requests)
  - [Rename](#rename)
  - [Set Description](#set-description)
- [DELETE Requests](#delete-requests)
  - [Delete Document](#delete-document)
- [Errors](#errors)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `documents` |
| `folder` | number | The folder ID |

---

## GET Requests

### Single Document

Retrieve a single document by ID.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | The document ID |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id}
```

```http
GET /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

**Success Response**

```json
{
  "id": number,
  "description": string,
  "file_name": string,
  "size": number,
  "modified": string,
  "modified_by": string,
  "mime_type": string,
  "permissions": {
    "can_edit": boolean,
    "can_delete": boolean
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Document ID |
| `description` | string | Document description |
| `file_name` | string | File name |
| `size` | number | File size in bytes |
| `modified` | string | ISO-8601 date (YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `modified_by` | string | Email of user who last modified |
| `mime_type` | string | MIME type of the file |
| `permissions.can_edit` | boolean | Whether user can edit |
| `permissions.can_delete` | boolean | Whether user can delete |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "id"
}
```

---

### All Documents

Retrieve all documents in a folder with pagination.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | Yes | Page number |
| `max_per_page` | number | No | Maximum items per page (uses default if not specified) |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&page={page_number}
```

```http
GET /WorldClientAPI/documents/{folder_id}/page/{page_number}?session={session_id}
```

**Success Response**

```json
{
  "documents": [],
  "permissions": {
    "can_edit": boolean,
    "can_delete": boolean,
    "can_write": boolean
  },
  "sort_column": string,
  "sort_ascending": boolean,
  "page": number,
  "pages": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `documents` | array | Array of document objects |
| `permissions` | object | Folder-level permissions |
| `sort_column` | string | Current sort column |
| `sort_ascending` | boolean | Sort direction |
| `page` | number | Current page number |
| `pages` | number | Total number of pages |

---

### Sort Documents

Change the sort order when retrieving documents.

**Sort Column Options**

- `file_name`
- `size`
- `modified`
- `modified_by`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sort_column` | string | Yes | Column to sort by |
| `sort_ascending` | boolean | Yes | Sort direction |
| `page` | number | Yes | Page number |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}
```

```http
GET /WorldClientAPI/documents/{folder_id}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}
```

**Success Response**

```json
{
  "documents": []
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "sort_column"
}
```

---

### Search Documents

Search documents by file name or modified by user.

The search value searches on the following properties:
- `file_name`
- `modified_by`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | string | Yes | Search query |
| `page` | number | Yes | Page number |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&page={page_number}&search={string}
```

```http
GET /WorldClientAPI/documents/{folder_id}/page/{page_number}?session={session_id}&search={string}
```

**Success Response**

```json
{
  "documents": []
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "search"
}
```

---

### Download Document

Download a document file.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID |
| `download` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id}&download=1
```

```http
GET /WorldClientAPI/documents/{folder_id}/document/{document_id}/download?session={session_id}
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

### Move

Move documents to another folder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID(s), comma-separated for multiple |
| `move` | number | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id1,document_id2}
```

```http
POST /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

```http
POST /WorldClientAPI/documents/{folder_id}/document/ids?session={session_id}&id={document_id1,document_id2}
```

**Request Body**

```json
{
  "move": number
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
  "error": "InvalidParameter",
  "parameter": "move"
}
```

---

### Copy

Copy documents to another folder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID(s), comma-separated for multiple |
| `copy` | number | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id1,document_id2}
```

```http
POST /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

```http
POST /WorldClientAPI/documents/{folder_id}/document/ids?session={session_id}&id={document_id1,document_id2}
```

**Request Body**

```json
{
  "copy": number
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
  "error": "InvalidParameter",
  "parameter": "copy"
}
```

---

### Upload Document

Upload one or more documents to a folder.

This request handles multiple files at once.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `overwrite` | boolean | No | Overwrite existing file of the same name |

If there's a name match and the `overwrite` flag is not passed, the file will have parentheses with a number added at the end of the name.

**Example:** `temp.txt` becomes `temp(1).txt`

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&overwrite=true
```

```http
POST /WorldClientAPI/documents/{folder_id}?session={session_id}&overwrite=true
```

**Success Response**

```json
{
  "success": true,
  "id": number
}
```

**Error Response**

```json
{
  "error": string
}
```

---

## PUT Requests

### Rename

Rename a document.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID |
| `rename` | string | Yes | New file name |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id}
```

```http
PUT /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

**Request Body**

```json
{
  "rename": string
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
  "error": "InvalidParameter",
  "parameter": "rename"
}
```

---

### Set Description

Set the description for a document.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID |
| `description` | string | Yes | New description |

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id}
```

```http
PUT /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

**Request Body**

```json
{
  "description": string
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
  "error": "InvalidParameter",
  "parameter": "description"
}
```

---

## DELETE Requests

### Delete Document

Delete one or more documents.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Document ID(s), comma-separated for multiple |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=documents&folder={folder_id}&id={document_id1,document_id2}
```

```http
DELETE /WorldClientAPI/documents/{folder_id}/document/{document_id}?session={session_id}
```

```http
DELETE /WorldClientAPI/documents/{folder_id}/document/ids?session={session_id}&id={document_id1,document_id2}
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

## Errors

| Error | Description |
|-------|-------------|
| `InvalidParameter` | The parameter value was invalid |
| `InadequatePermissions` | The action is not permitted for this user |
| `InvalidRequest` | Invalid parameters in the request |
| `ContentBlocked` | The uploaded file type is blocked |
| `Oversized` | The uploaded file is too large |
| `UploadRefused` | There was a problem with the uploaded file |
