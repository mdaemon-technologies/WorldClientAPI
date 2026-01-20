# Messages

> Message operations including retrieval, threading, search, status management, and attachments.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required URI Parameters](#required-uri-parameters)
- **GET Requests**
  - [Single Message](#single-message)
  - [Next Unread](#next-unread)
  - [All Messages](#all-messages)
    - [Threaded Messages](#threaded-messages)
  - [All Flagged Messages](#all-flagged-messages)
  - [All Unread Messages](#all-unread-messages)
  - [All Snoozed Messages](#all-snoozed-messages)
  - [Sort Messages](#sort-messages)
  - [Search Messages](#search-messages)
  - [Saved Searches](#saved-searches)
  - [Message Source](#message-source)
  - [Download Attachment](#download-attachment)
  - [Download All Attachments](#download-all-attachments)
  - [Quotas](#quotas)
- **POST Requests**
  - [Move](#move)
  - [Copy](#copy)
  - [Spam](#spam)
  - [Read](#read)
  - [Flagged](#flagged)
  - [Undelete](#undelete)
  - [Expand Thread](#expand-thread)
  - [Collapse Thread](#collapse-thread)
  - [Categories](#categories)
  - [Snooze](#snooze)
  - [Recall](#recall)
  - [Send Read Confirmation](#send-read-confirmation)
  - [Accept Invite](#accept-invite)
  - [Tentatively Accept Invite](#tentatively-accept-invite)
  - [Decline Invite](#decline-invite)
  - [Import Messages to Folder](#import-messages-to-folder)
  - [Import Message to Inbox](#import-message-to-inbox)
  - [Import ICS Event](#import-ics-event)
  - [Import VCard Contact](#import-vcard-contact)
  - [Import S/MIME Public Key to Contact](#import-smime-public-key-to-contact)
- **PUT Requests**
  - [Saved Searches (PUT)](#saved-searches-put)
- **DELETE Requests**
  - [Delete Messages](#delete-messages)
  - [Delete Attachment](#delete-attachment)
  - [Delete All Attachments](#delete-all-attachments)
- [Errors](#errors)

---

## Required URI Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `messages` |
| `folder` | number | Folder ID |

---

## GET Requests

### Single Message

Retrieve a single message by ID.

#### GET `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `allow_remote_images` | boolean | No | Allow remote images in message body |
| `allow_hyper_links` | boolean | No | Allow hyperlinks in message body |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&allow_remote_images={boolean}&allow_hyper_links={boolean}
```

```http
GET /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}&allow_remote_images={boolean}&allow_hyper_links={boolean}
```

**Success Response** - `200 OK`

```json
{
  "id": number,
  "headers": {
    "to": string,
    "cc": string,
    "bcc": string,
    "from": string,
    "subject": string,
    "date": string,
    "list_unsubscribe": string,
    "list_unsubscribe_post": string
  },
  "dkim_verified": string,
  "mdpgp_result": string,
  "mdpgp_result_type": string,
  "body": string,
  "categories": string,
  "status": {
    "unread": boolean,
    "new": boolean,
    "replied": boolean,
    "replied_date": string,
    "forwarded": boolean,
    "forwarded_date": string,
    "flagged": boolean,
    "deleted": boolean,
    "snoozed": string
  },
  "urgent": boolean,
  "size": number,
  "meeting": {
    "invite": boolean,
    "retraction": boolean,
    "response": boolean
  },
  "folder": number,
  "remote_images_blocked": boolean,
  "hyper_links_disabled": boolean,
  "can_recall": boolean,
  "deferred_date": string,
  "attachments": [
    {
      "id": number,
      "link": string,
      "name": string,
      "size": number,
      "mime_type": string
    }
  ],
  "permissions": {
    "can_read": boolean,
    "can_mark_seen": boolean,
    "can_edit": boolean,
    "can_delete": boolean
  },
  "tracking_url": string,
  "read_receipt": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Message ID |
| `headers.to` | string | To recipients |
| `headers.cc` | string | CC recipients |
| `headers.bcc` | string | BCC recipients |
| `headers.from` | string | Sender address |
| `headers.subject` | string | Message subject |
| `headers.date` | string | ISO-8601 date (YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `headers.list_unsubscribe` | string | List-Unsubscribe header value (RFC 8058) |
| `headers.list_unsubscribe_post` | string | List-Unsubscribe-Post header value (RFC 8058) |
| `dkim_verified` | string | Verified DKIM domain (only within 7 days of verification) |
| `mdpgp_result` | string | X-MDPGP-Results header explanation |
| `mdpgp_result_type` | string | `v` for verified, `d` for decrypted |
| `body` | string | Message body (HTML or plain text) |
| `categories` | string | Comma-delimited categories |
| `status.unread` | boolean | Message is unread |
| `status.new` | boolean | Message is new |
| `status.replied` | boolean | Message has been replied to |
| `status.replied_date` | string | ISO-8601 date of reply or empty string |
| `status.forwarded` | boolean | Message has been forwarded |
| `status.forwarded_date` | string | ISO-8601 date of forward or empty string |
| `status.flagged` | boolean | Message is flagged |
| `status.deleted` | boolean | IMAP delete flag |
| `status.snoozed` | string | ISO-8601 date (only if snoozed) |
| `urgent` | boolean | Message is urgent |
| `size` | number | Size in bytes |
| `meeting.invite` | boolean | Message is a meeting invite |
| `meeting.retraction` | boolean | Message is a meeting retraction |
| `meeting.response` | boolean | Message is a meeting response |
| `folder` | number | Folder ID (only for search results) |
| `remote_images_blocked` | boolean | Remote/inline images blocked by default |
| `hyper_links_disabled` | boolean | Links blocked in spam or SPF-failed messages |
| `can_recall` | boolean | Message can be recalled (sent items only) |
| `deferred_date` | string | ISO-8601 date or empty string |
| `attachments` | array | List of attachments |
| `permissions.can_read` | boolean | User can read message |
| `permissions.can_mark_seen` | boolean | User can mark message as seen |
| `permissions.can_edit` | boolean | User can edit message |
| `permissions.can_delete` | boolean | User can delete message |
| `tracking_url` | string | Package tracking URL (FedEx, UPS, USPS, DHL, OnTrac) |
| `read_receipt` | boolean | Prompt user to send read confirmation |

**Error Response**

```json
{
  "error": string
}
```

---

### Next Unread

Get the next unread message given the current message ID. If there is no unread message, the current message is returned.

#### GET `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Current message ID |
| `next_unread` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&next_unread=1
```

```http
GET /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}&next_unread=1
```

**Success Response** - `200 OK`

Returns the same structure as [Single Message](#single-message).

**Error Response**

```json
{
  "error": string
}
```

---

### All Messages

Retrieve all messages in a folder with pagination.

#### GET `/WorldClientAPI/messages/{folder_id}/page/{page_number}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number (defaults to first page) |
| `max_per_page` | number | No | Maximum messages per page (uses default setting if omitted) |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&max_per_page={max_messages_per_page}
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}?session={session_id}&max_per_page={max_messages_per_page}
```

**Success Response** - `200 OK`

```json
{
  "messages": [],
  "permissions": {
    "can_edit": boolean,
    "can_create": boolean,
    "can_delete": boolean,
    "can_write": boolean,
    "can_read": boolean,
    "can_mark_seen": boolean
  },
  "sort_column": string,
  "sort_ascending": boolean,
  "page": number,
  "pages": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `messages` | array | Array of message objects (see below) |
| `permissions` | object | Folder permissions |
| `sort_column` | string | Current sort column |
| `sort_ascending` | boolean | Sort order |
| `page` | number | Current page number |
| `pages` | number | Total number of pages |

The `messages` array contains message objects like Single Message but **without**: `remote_images_blocked`, `hyper_links_disabled`, `body`, `attachments`, `permissions`.

**Additional property in list items:**

| Field | Type | Description |
|-------|------|-------------|
| `has_attachments` | boolean | Message has attachments |

**Error Response**

```json
{
  "error": string
}
```

---

### Threaded Messages

Message threading is supported by WorldClientAPI. To enable threading:

```http
PUT /WorldClientAPI/settings?session={session_id}&ThreadedMessageView=Yes
```

When retrieving a page with threading enabled, messages include an additional `thread` property:

```json
{
  "thread": {
    "collapsed": boolean,
    "depth": number,
    "has_children": boolean,
    "has_unread_children": boolean,
    "index": number,
    "parent_index": number
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `collapsed` | boolean | Thread is collapsed |
| `depth` | number | Nesting depth |
| `has_children` | boolean | Message has child messages |
| `has_unread_children` | boolean | Message has unread children |
| `index` | number | Thread index |
| `parent_index` | number | Parent thread index |

To retrieve immediate children of a message, add `parent_id` parameter:

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&parent_id={number}
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}?session={session_id}&parent_id={number}
```

When [expanding](#expand-thread) or [collapsing](#collapse-thread) a thread, be sure to notify the server.

---

### All Flagged Messages

Retrieve all flagged messages in a folder.

#### GET `/WorldClientAPI/messages/{folder_id}/page/{page_number}/flagged`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string | Yes | Must be `flagged` |
| `page` | number | No | Page number |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&status=flagged
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}/flagged?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "messages": []
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "status"
}
```

---

### All Unread Messages

Retrieve all unread messages in a folder.

#### GET `/WorldClientAPI/messages/{folder_id}/page/{page_number}/unread`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string | Yes | Must be `unread` |
| `page` | number | No | Page number |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&status=unread
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}/unread?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "messages": []
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "status"
}
```

---

### All Snoozed Messages

Retrieve all snoozed messages in a folder.

#### GET `/WorldClientAPI/messages/{folder_id}/page/{page_number}/snoozed`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string | Yes | Must be `snoozed` |
| `page` | number | No | Page number |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&status=snoozed
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}/snoozed?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "messages": []
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "status"
}
```

---

### Sort Messages

Change the sort order and sort column for message listing.

#### GET `/WorldClientAPI/messages/{folder_id}/page/{page_number}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sort_column` | string | Yes | Sort column (see options below) |
| `sort_ascending` | boolean | Yes | Sort order |
| `page` | number | No | Page number |

**Sort Column Options:**

- `size`
- `date`
- `sender`
- `recipient`
- `subject`
- `categories`

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}
```

```http
GET /WorldClientAPI/messages/{folder_id}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}
```

**Success Response** - `200 OK`

```json
{
  "messages": []
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

### Search Messages

Search for messages with various criteria.

#### GET `/WorldClientAPI/messages/{folder_id}/search`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | boolean | Yes | Enable search mode |
| `quick` | string | No | Quick search (searches subject, from, to, categories) |
| `from` | string | No | Search sender (ignored if `quick` is used) |
| `to` | string | No | Search recipient |
| `subject` | string | No | Search subject |
| `body` | string | No | Search body |
| `categories` | string | No | Search categories |
| `attachment` | string | No | Search attachment name |
| `has_attachment` | any | No | Filter messages with attachments (any defined value) |
| `unread` | any | No | Filter unread messages (any defined value) |
| `flagged` | any | No | Filter flagged messages (any defined value) |
| `snoozed` | any | No | Filter snoozed messages (any defined value) |
| `start` | string | No | Start date (ISO-8601: YYYY-MM-DDTHH:mm+/-HH:mm) |
| `end` | string | No | End date (ISO-8601: YYYY-MM-DDTHH:mm+/-HH:mm) |
| `search_subs` | number | No | Parent folder ID (for sub-folder search) |
| `cancel_search` | boolean | No | Cancel search and return all messages |

> To search all folders or sub-folders, send the "search results folder" folder ID retrieved via the settings route with `SearchResultsFolderId`.

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&search=1&quick={string}
```

```http
GET /WorldClientAPI/messages/{folder_id}/search?session={session_id}&quick={string}
```

To cancel search:

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&search=1&cancel_search=1
```

```http
GET /WorldClientAPI/messages/{folder_id}/search?session={session_id}&cancel_search=1
```

**Success Response** - `200 OK`

```json
{
  "messages": []
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

### Saved Searches

Get an array of saved search objects.

#### GET `/WorldClientAPI/messages/0/saved_search`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `folder` | number | Yes | Must be `0` |
| `saved_search` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder=0&saved_search=1
```

```http
GET /WorldClientAPI/messages/0/saved_search?session={session_id}
```

**Success Response** - `200 OK`

```json
[
  {
    "id": number,
    "name": string,
    "searchData": {
      "SearchFrom": string,
      "SearchTo": string,
      "SearchSubject": string,
      "SearchCategories": string,
      "SearchBody": string,
      "SearchAttachmentName": string,
      "SearchAllDates": boolean,
      "SearchAfterDate": number,
      "SearchBeforeDate": number,
      "SearchThroughToday": boolean,
      "SearchHasAttachment": boolean,
      "SearchIsUnread": boolean,
      "SearchIsFlagged": boolean,
      "SearchAllFolders": string
    }
  }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Saved search ID |
| `name` | string | Saved search name |
| `searchData.SearchFrom` | string | From filter |
| `searchData.SearchTo` | string | To filter |
| `searchData.SearchSubject` | string | Subject filter |
| `searchData.SearchCategories` | string | Categories filter |
| `searchData.SearchBody` | string | Body filter |
| `searchData.SearchAttachmentName` | string | Attachment name filter |
| `searchData.SearchAllDates` | boolean | Search all dates |
| `searchData.SearchAfterDate` | number | UTC time in milliseconds |
| `searchData.SearchBeforeDate` | number | UTC time in milliseconds |
| `searchData.SearchThroughToday` | boolean | Search through today |
| `searchData.SearchHasAttachment` | boolean | Has attachment filter |
| `searchData.SearchIsUnread` | boolean | Unread filter |
| `searchData.SearchIsFlagged` | boolean | Flagged filter |
| `searchData.SearchAllFolders` | string | `0` = current folder, `1` = all folders, `2` = sub folders |

**Error Response**

```json
{
  "error": string
}
```

---

### Message Source

Get the raw source of a message.

#### GET `/WorldClientAPI/messages/{folder_id}/message/{message_id}/source`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `source` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&source=1
```

```http
GET /WorldClientAPI/messages/{folder_id}/message/{message_id}/source?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "source": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `source` | string | Raw message source (can be converted to a Blob) |

**Error Response**

```json
{
  "error": string
}
```

---

### Download Attachment

Download a single attachment from a message.

#### GET `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&attachment={attachment_id}
```

```http
GET /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}?session={session_id}
```

**Success Response**

Download begins.

**Error Responses**

| Status | Description |
|--------|-------------|
| 404 | Not Found |
| 500 | Internal Server Error |

---

### Download All Attachments

Download all attachments from a message as a zip file.

#### GET `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachments`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachments` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&attachments=1
```

```http
GET /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachments?session={session_id}
```

**Success Response**

Download begins.

**Error Responses**

| Status | Description |
|--------|-------------|
| 404 | Not Found |
| 500 | Internal Server Error |

---

### Quotas

Get user quota information. No folder is necessary for this request since it calculates values for the total of all folders combined.

#### GET `/WorldClientAPI/messages/quotas`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `quotas` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=messages&quotas=1
```

```http
GET /WorldClientAPI/messages/quotas
```

**Success Response** - `200 OK`

When quotas are not enabled:

```json
{
  "quotas": {}
}
```

When quotas are enabled:

```json
{
  "quotas": {
    "space": {
      "max": number,
      "used": number,
      "percent_used": number
    },
    "files": {
      "max": number,
      "used": number,
      "percent_used": number
    }
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `quotas.space.max` | number | Maximum disk space |
| `quotas.space.used` | number | Used disk space |
| `quotas.space.percent_used` | number | Percentage of disk space used |
| `quotas.files.max` | number | Maximum number of files |
| `quotas.files.used` | number | Number of files used |
| `quotas.files.percent_used` | number | Percentage of files used |

**Error Responses**

| Status | Description |
|--------|-------------|
| 404 | Not Found |
| 500 | Internal Server Error |

---

## POST Requests

### Move

Move one or more messages to another folder.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `move` | number | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

move={move_to_this_folder_id}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

move={move_to_this_folder_id}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

move={move_to_this_folder_id}
```

**Success Response** - `200 OK`

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

Copy one or more messages to another folder.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `copy` | number | Yes | Destination folder ID |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

copy={copy_to_this_folder_id}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

copy={copy_to_this_folder_id}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc}
Content-Type: application/x-www-form-urlencoded

copy={copy_to_this_folder_id}
```

**Success Response** - `200 OK`

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

### Spam

Mark one or more messages as spam or not spam.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `spam` | boolean | Yes | `true` to mark as spam, `false` for not spam |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

spam=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

spam=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

spam=1
```

**Success Response** - `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "spam"
}
```

---

### Read

Mark one or more messages as read or unread.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `read` | boolean | Yes | `true` for read, `false` for unread |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages
Content-Type: application/x-www-form-urlencoded

folder={folder_id}&id={message_id}&read={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

read={boolean}
```

**Success Response** - `200 OK`

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

### Flagged

Mark one or more messages as flagged or unflagged.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `flagged` | boolean | Yes | `true` for flagged, `false` for unflagged |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

flagged={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

flagged={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

flagged={boolean}
```

**Success Response** - `200 OK`

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

### Undelete

Undelete messages that were deleted with IMAP delete. This can only be used when `UseIMAPDelete` is set to `Yes` and `HideDeletedMessages` is set to `No` for the user.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `undelete` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

undelete=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

undelete=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

undelete=1
```

**Success Response** - `200 OK`

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

### Expand Thread

Notify the server that a thread is being expanded. Required when threading is enabled and you load children of a thread so that full page requests include the children.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Parent message ID |
| `expand` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

expand=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

expand=1
```

**Success Response** - `200 OK`

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

### Collapse Thread

Notify the server that a thread is being collapsed.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Parent message ID |
| `collapse` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

collapse=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

collapse=1
```

**Success Response** - `200 OK`

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

### Categories

Set categories for one or more messages.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `categories` | string | Yes | Comma-delimited categories |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
Content-Type: application/x-www-form-urlencoded

categories={category1,category2,etc.}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

categories={category1,category2,etc.}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
Content-Type: application/json

{
  "categories": ["category1", "category2"]
}
```

**Success Response** - `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "categories"
}
```

---

### Snooze

Snooze one or more messages until a specified time.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `snooze` | string | Yes | ISO-8601 date (YYYY-MM-DDTHH:mm+/-HH:mm) or `0` to unsnooze |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

snooze={snooze_until}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

snooze={snooze_until}
```

**Success Response** - `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "snooze"
}
```

---

### Recall

Recall a sent message.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `recall` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

recall=1
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

recall=1
```

**Success Response** - `200 OK`

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

### Send Read Confirmation

Send or decline a read confirmation for a message. If `read_receipt` is requested and the `SendReadConfirmations` user option is `prompt`, the user should be prompted to confirm.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `send_read_confirm` | boolean | Yes | `true` to send, `false` to decline |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

send_read_confirm=true
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

send_read_confirm=false
```

**Success Response** - `200 OK`

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

> If you do not send anything, the `read_receipt` property will continue to be returned when the message is requested.

---

### Accept Invite

Accept a meeting invitation.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `accept` | number | Yes | Must be `1` |
| `send_response` | boolean | Yes | Send response to organizer |
| `body` | string | No | Additional message body |
| `edit` | boolean | No | Return event ID for editing |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

accept=1&send_response={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

accept=1&send_response={boolean}
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "event_id": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Operation succeeded |
| `event_id` | number | Event ID in default calendar (only if `edit=true`) |

**Error Response**

```json
{
  "error": string
}
```

---

### Tentatively Accept Invite

Tentatively accept a meeting invitation. Sets the `busy_status` of the event to tentative.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `tentative` | number | Yes | Must be `1` |
| `send_response` | boolean | Yes | Send response to organizer |
| `body` | string | No | Additional message body |
| `edit` | boolean | No | Return event ID for editing |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

tentative=1&send_response={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

tentative=1&send_response={boolean}
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "event_id": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Operation succeeded |
| `event_id` | number | Event ID in default calendar (only if `edit=true`) |

**Error Response**

```json
{
  "error": string
}
```

---

### Decline Invite

Decline a meeting invitation. When a meeting cancellation message is received (`meeting.retraction`), sending this POST will remove the meeting from the user's calendar.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `decline` | number | Yes | Must be `1` |
| `send_response` | boolean | Yes | Send response to organizer |
| `body` | string | No | Additional message body |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

decline=1&send_response={boolean}
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

decline=1&send_response={boolean}
```

**Success Response** - `200 OK`

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

### Import Messages to Folder

Import `msg`, `eml`, or `message/rfc822` files by uploading them from a file upload dialog.

#### POST `/WorldClientAPI/messages/import/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `import` | number | Yes | Must be `1` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages
Content-Type: multipart/form-data

folder={folder_id}&import=1
[file upload]
```

```http
POST /WorldClientAPI/messages/import/{folder_id}
Content-Type: multipart/form-data

[file upload]
```

See [How to upload files](uploads.md).

**Success Response** - `200 OK`

```json
{
  "success": true,
  "messages": [
    {
      "success": boolean,
      "subject": string,
      "name": string
    }
  ]
}
```

Or for failed uploads:

```json
{
  "success": true,
  "messages": [
    {
      "fail": string,
      "name": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `messages[].success` | boolean | Import succeeded |
| `messages[].subject` | string | Subject of imported message |
| `messages[].name` | string | Name of uploaded file |
| `messages[].fail` | string | Error message for failed upload |

**Error Response**

```json
{
  "error": "InadequatePermissions"
}
```

```json
{
  "error": "UploadRefused"
}
```

---

### Import Message to Inbox

Import an attached RFC822 email message to the user's Inbox. The file must have a MIME type of `message/rfc822`.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |
| `import` | string | Yes | Must be `message` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

attachment={attachment_id}&import=message
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}
Content-Type: application/x-www-form-urlencoded

import=message
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "subject": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `subject` | string | Subject of imported message |

**Error Response**

```json
{
  "error": string
}
```

---

### Import ICS Event

Import an attached Calendar Event (.ics) to the default Calendar. The file must have a MIME type of `text/calendar` or `application/ics`.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |
| `import` | string | Yes | Must be `ics` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

attachment={attachment_id}&import=ics
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}
Content-Type: application/x-www-form-urlencoded

import=ics
```

**Success Response** - `200 OK`

```json
{
  "importResult": number,
  "itemsRead": number,
  "itemsAdded": number,
  "itemsChanged": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `importResult` | number | `0` = Success, `9` = File error, `11` = Invalid user |
| `itemsRead` | number | Number of items read |
| `itemsAdded` | number | Number of items added |
| `itemsChanged` | number | Number of items changed |

**Error Response**

```json
{
  "error": string
}
```

---

### Import VCard Contact

Import an attached VCard (.vcf) to the default contacts folder. The file must have a MIME type of `text/vcard` or `text/x-vcard`.

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |
| `import` | string | Yes | Must be `vcard` |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

attachment={attachment_id}&import=vcard
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}
Content-Type: application/x-www-form-urlencoded

import=vcard
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "contact": {
    "email": string,
    "name": string
  },
  "updated": boolean,
  "exists": boolean,
  "added": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| `contact.email` | string | Contact email address |
| `contact.name` | string | Contact name |
| `updated` | boolean | UUID matched existing contact (contact was updated) |
| `exists` | boolean | Address matched existing contact |
| `added` | boolean | Contact was added |

**Error Response**

```json
{
  "error": string
}
```

---

### Import S/MIME Public Key to Contact

Import a valid S/MIME public key to the contact with the from address listed.

- `full` - Default contacts folder
- `global` - Default public address book for the domain (requires admin permissions)

#### POST `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |
| `import` | string | Yes | Must be `key` |
| `contacts_folder` | string | Yes | Contact folder ID, `full`, or `global` |
| `guid` | string | No | Contact GUID to add key to (contacts can have multiple keys) |

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}
Content-Type: application/x-www-form-urlencoded

attachment={attachment_id}&import=key&contacts_folder=full
```

```http
POST /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}?session={session_id}
Content-Type: application/x-www-form-urlencoded

import=key
```

**Success Response** - `200 OK`

```json
{
  "success": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | string | Success message |

**Error Response**

```json
{
  "error": string
}
```

---

## PUT Requests

### Saved Searches (PUT)

Replace the saved searches on the server.

#### PUT `/WorldClientAPI/messages/0/saved_search`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `folder` | number | Yes | Must be `0` |
| `saved_search` | number | Yes | Must be `1` |
| `saved_searches` | array | Yes | Base64-encoded or JSON array of saved searches |

**Request Body**

```json
[
  {
    "id": number,
    "name": string,
    "searchData": {
      "SearchFrom": string,
      "SearchTo": string,
      "SearchSubject": string,
      "SearchCategories": string,
      "SearchBody": string,
      "SearchAttachmentName": string,
      "SearchAllDates": boolean,
      "SearchAfterDate": number,
      "SearchBeforeDate": number,
      "SearchThroughToday": boolean,
      "SearchHasAttachment": boolean,
      "SearchIsUnread": boolean,
      "SearchIsFlagged": boolean,
      "SearchAllFolders": string
    }
  }
]
```

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=messages&folder=0&saved_search=1
Content-Type: application/x-www-form-urlencoded

saved_searches={array}
```

```http
PUT /WorldClientAPI/messages/0/saved_search?session={session_id}
Content-Type: application/x-www-form-urlencoded

saved_searches={array}
```

**Success Response** - `200 OK`

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

### Delete Messages

Delete one or more messages.

#### DELETE `/WorldClientAPI/messages/{folder_id}/message/{message_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Message ID(s), comma-delimited for multiple |
| `permanent` | number | No | Set to `1` to skip Deleted Items folder |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id1,message_id2,etc.}
```

```http
DELETE /WorldClientAPI/messages/{folder_id}/message/{message_id}?session={session_id}
```

```http
DELETE /WorldClientAPI/messages/{folder_id}/message/ids?session={session_id}&id={message_id1,message_id2,etc.}
```

**Success Response** - `200 OK`

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

### Delete Attachment

Delete a single attachment from a message. When an attachment is deleted, the message is changed and treated as a new message by the server, so the new `folderId` and `messageId` are returned.

#### DELETE `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | number | Yes | Attachment ID |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&attachment={attachment_id}
```

```http
DELETE /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/{attachment_id}?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "folderId": number,
  "messageId": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `folderId` | number | New folder ID |
| `messageId` | number | New message ID |

**Error Response**

```json
{
  "error": string
}
```

---

### Delete All Attachments

Delete all attachments from a message. The message is changed and treated as a new message by the server.

#### DELETE `/WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/all`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Message ID |
| `attachment` | string | Yes | Must be `all` |

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=messages&folder={folder_id}&id={message_id}&attachment=all
```

```http
DELETE /WorldClientAPI/messages/{folder_id}/message/{message_id}/attachment/all?session={session_id}
```

**Success Response** - `200 OK`

```json
{
  "success": true,
  "folderId": number,
  "messageId": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `folderId` | number | New folder ID |
| `messageId` | number | New message ID |

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
| `InvalidParameter` | The parameter value had an invalid value |
| `InadequatePermissions` | The attempted action is not permitted for this user |
| `InvalidRequest` | Invalid parameters in the request |
| `MissingParameter` | Required parameters missing from the request |
| `InvalidAction` | The requested action could not be completed on the item |
