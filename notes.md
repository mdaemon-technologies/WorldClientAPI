[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

# Notes

## Requests

### GET
- [Single Note](#single-note)
- [All Notes](#all-notes)
- [Sort Notes](#sort-notes)
- [Search Notes](#search-notes)
- [Download Attachment](#download-attachment)

### POST
- [Upload Attachment](#upload-attachment)
- [Move](#move)
- [Copy](#copy)
- [Attach Message Attachments to a New Note](#attach-message-attachments-to-a-new-note)

### PUT
- [Create Note](#create-note)
- [Update Note](#update-note)

### DELETE
- [Delete Note](#delete-note)
- [Delete Attachment](#delete-attachment)

---

## Required URI Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| route | string | `notes` |
| folder | number | folder_id |

---

## GET

### Single Note

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |

**Responses**

*success*

```json
{
    "id": number,
    "body": string, // text/plain formatted string
    "body_html": string, // text/HTML formatted string
    "color": number, // 0 = Blue, 1 = Green, 2 = Pink, 3 = Yellow, 4 = White
    "categories": string, // comma delimited list
    "contacts": string, // comma delimited list of contacts
    "created_by": string, // email address of creator
    "created": string, // ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm
    "modified": string, // ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm
    "top": number,
    "left": number,
    "height": number,
    "width": number,
    "attachments": [
        {
            "name": string,
            "mime_type": string,
            "size": number, // bytes
            "id": number
        }
    ],
    "permissions": {
        "can_edit": boolean,
        "can_delete": boolean
    }
}
```

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id}
```

or

```
GET /WorldClientAPI/notes/{folder_id}/note/{note_id}?session={session_id}
```

---

### All Notes

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| page | number | Page number |
| max_per_page | number | Optional - default setting exists |

**Responses**

*success*

```json
{
    "notes": [ ],
    "permissions": {
        "can_read": boolean,
        "can_create": boolean,
        "can_edit": boolean,
        "can_write": boolean,
        "can_delete": boolean
    },
    "sort_column": string,
    "sort_ascending": boolean,
    "page": number, // current page
    "pages": number // total pages
}
```

`notes` is filled with note objects like above minus the following properties:

- attachments
- contacts

plus the following properties:

| Property | Type | Description |
|----------|------|-------------|
| has_attachments | boolean | Whether the note has attachments |

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&page={page_number}
```

or

```
GET /WorldClientAPI/notes/{folder_id}/page/{page_number}?session={session_id}
```

---

### Sort Notes

To change the sort order and sort column, include these parameters with any GET request for multiple notes.

`sort_column` options: `"body"`, `"created"`, `"modified"`, `"color"`, `"categories"`

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| sort_column | string | Column to sort by |
| sort_ascending | boolean | Sort direction |
| page | number | Page number |

**Responses**

*success*

```json
{
    "notes": [ ] // filled with note objects like the All Notes GET
}
```

*failure*

```json
{
    "error": "InvalidParameter",
    "parameter": "sort_column"
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}
```

or

```
GET /WorldClientAPI/notes/{folder_id}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}
```

---

### Search Notes

The search value searches on the following properties:

- body
- categories

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| search | string | Search term |
| page | number | Page number |

**Responses**

*success*

```json
{
    "notes": [ ] // filled with note objects like the All Notes GET
}
```

*failure*

```json
{
    "error": "InvalidParameter",
    "parameter": "search"
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&page={page_number}&search={string}
```

or

```
GET /WorldClientAPI/notes/{folder_id}/page/{page_number}?session={session_id}&search={string}
```

---

### Download Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |
| attachment | number | attachment_id |

or

| Parameter | Type | Description |
|-----------|------|-------------|
| download | number | attachment_id (deprecated) |

**Responses**

*success*

Download begins.

*failure*

- `401 Unauthorized`
- `404 Not Found`
- `500 Internal Server Error`

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id}&attachment={attachment_id}
```

or

```
GET /WorldClientAPI/notes/{folder_id}/note/{note_id}/attachment/{attachment_id}?session={session_id}
```

---

## POST

### Upload Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id (for saved notes) |
| new | number | 1 (for unsaved notes) |
| attachment | number | 1 |

or

| Parameter | Type | Description |
|-----------|------|-------------|
| upload | number | 1 (deprecated) |

**Responses**

*success (existing note)*

```json
[ ... ] // array of attachment objects
```

*success (new note - unsaved)*

```json
[
    {
        "mime_type": string,
        "name": string,
        "size": number, // bytes
        "temp": string // temporary file name on the server
    }
]
```

When the note is sent to the server, the attachments will be added to the note.

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

Known Note:

```
POST /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id}&attachment=1
```

or

```
POST /WorldClientAPI/notes/{folder_id}/note/{note_id}/attachment?session={session_id}
```

New Note:

```
POST /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&new=1&attachment=1
```

or

```
POST /WorldClientAPI/notes/{folder_id}/new/attachment?session={session_id}
```

---

### Move

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |
| move | number | folder_id to move to |

**Responses**

*success*

```json
{
    "success": true
}
```

*failure*

```json
{
    "error": "InvalidParameter",
    "parameter": "move"
}
```

**Example Request**

```
POST /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id1,note_id2}
```

or

```
POST /WorldClientAPI/notes/{folder_id}/note/{note_id1,note_id2}?session={session_id}
```

or

```
POST /WorldClientAPI/notes/{folder_id}/note/ids?session={session_id}&id={note_id1,note_id2,etc.}
```

POST data:

```
move={move_to_this_folder_id}
```

---

### Copy

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |
| copy | number | folder_id to copy to |

**Responses**

*success*

```json
{
    "success": true
}
```

*failure*

```json
{
    "error": "InvalidParameter",
    "parameter": "copy"
}
```

**Example Request**

```
POST /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id1,note_id2,etc.}
```

or

```
POST /WorldClientAPI/notes/{folder_id}/note/{note_id}?session={session_id}
```

or

```
POST /WorldClientAPI/notes/{folder_id}/note/ids?session={session_id}&id={note_id1,note_id2,etc.}
```

POST data:

```
copy={copy_to_this_folder_id}
```

---

### Attach Message Attachments to a New Note

Adds the attachments from a message to a new note. When the note is saved, include the attachments list, and they will be included in the new note.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| attach_from_message | number | 1 |
| mail_folder_id | number | Mail folder ID |
| message_id | number | Message ID |

**Responses**

*success*

```json
[
    {
        "name": string,
        "size": number,
        "mime_type": string,
        "temp": string // temporary file name on the server
    }
]
```

*failure*

```json
{
    "error": "Internal Server Error"
}
```

or

```json
{
    "error": "MissingParameter",
    "parameter": "mail_folder_id" // or "message_id"
}
```

or

```json
{
    "error": "InadequatePermissions"
}
```

**Example Request**

New Note:

```
POST /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&new=1&attachment=1
```

or

```
POST /WorldClientAPI/notes/{folder_id}/new/attachment?session={session_id}
```

POST data:

```
attach_from_message=1
mail_folder_id={number}
message_id={number}
```

---

## PUT

### Create Note

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| body | string | No | Plain text only |
| body_html | string | No | HTML allowed |
| color | number | No | Color code |
| contacts | string | No | Comma delimited list |
| categories | string | No | Comma delimited list |
| attachments | string | No | URI encoded attachments array (returned by upload requests with new=1) |
| top | number | No | Position |
| left | number | No | Position |
| height | number | No | Size |
| width | number | No | Size |

**Responses**

*success*

```json
{
    "success": true,
    "id": number // note_id
}
```

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}
```

or

```
PUT /WorldClientAPI/notes/{folder_id}?session={session_id}
```

---

### Update Note

When updating a note, submitting an empty property, such as `"body": ""` will result in the property being empty. However, if you do not submit the property at all, it will remain unchanged on the server.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | number | Yes | note_id |
| body | string | No | Plain text only |
| body_html | string | No | HTML allowed |
| color | number | No | Color code |
| contacts | string | No | Comma delimited list |
| categories | string | No | Comma delimited list |
| top | number | No | Position |
| left | number | No | Position |
| height | number | No | Size |
| width | number | No | Size |

**Responses**

*success*

```json
{
    "success": true
}
```

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id}
```

or

```
PUT /WorldClientAPI/notes/{folder_id}/note/{note_id}?session={session_id}
```

---

## DELETE

### Delete Note

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |

**Responses**

*success*

```json
{
    "success": true
}
```

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
DELETE /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id1,note_id2,etc.}
```

or

```
DELETE /WorldClientAPI/notes/{folder_id}/note/{note_id}?session={session_id}
```

or

```
DELETE /WorldClientAPI/notes/{folder_id}/note/ids?session={session_id}&id={note_id1,note_id2,etc}
```

---

### Delete Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | note_id |
| attachment | number | attachment_id |

**Responses**

*success*

```json
{
    "attachments": [ ... ] // array of remaining attachments
}
```

*failure*

```json
{
    "error": string // error_message
}
```

**Example Request**

```
DELETE /WorldClientAPI?session={session_id}&route=notes&folder={folder_id}&id={note_id}&attachment={attachment_id}
```

or

```
DELETE /WorldClientAPI/notes/{folder_id}/note/{note_id}/attachment/{attachment_id}?session={session_id}
```

---

## Errors

| Error | Description |
|-------|-------------|
| InvalidParameter | The parameter value returned had an invalid value |
| InadequatePermissions | The attempted action is not permitted on this note for this user |
| InvalidRequest | There were invalid parameters in the request |
