# Folders

> Folder operations, notifications, and access control lists.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [GET Requests](#get-requests)
  - [Single Folder](#single-folder)
  - [All Folders](#all-folders)
  - [All Folders of Type](#all-folders-of-type)
  - [Notification Folders](#get-notification-folders)
  - [Access Control List](#get-access-control-list)
- [POST Requests](#post-requests)
  - [Rename](#rename)
  - [Move](#move)
  - [Mark All Read](#mark-all-read)
- [PUT Requests](#put-requests)
  - [Create Folder](#create-folder)
  - [Favorite Toggle](#favorite-toggle)
  - [Favorite Nickname](#favorite-nickname)
  - [Favorite Order](#favorite-order)
  - [Subscribe Toggle](#subscribe-toggle)
  - [Collapse Toggle](#collapse-toggle)
  - [Checked Calendar Toggle](#checked-calendar-toggle)
  - [Notification Folders](#put-notification-folders)
  - [Access Control List](#put-access-control-list)
- [DELETE Requests](#delete-requests)
  - [Delete Folder](#delete-folder)
  - [Empty Deleted Items Folder](#empty-deleted-items-folder)
  - [Delete All Messages](#delete-all-messages)
- [Additional Errors](#additional-errors)
- [Important Notes](#important-notes)

---

## Required Parameter

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `folders` |

---

## GET Requests

### Single Folder

#### GET `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |

**Example Request**

```http
GET /WorldClientAPI?route=folders&id={folder_id}&session={session_id}
```

```http
GET /WorldClientAPI/folders/{folder_id}?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "id": number,
  "display_name": string,
  "path": string,
  "parent_id": number,
  "depth": number,
  "has_subs": boolean,
  "admin": boolean,
  "has_new": boolean,
  "permissions": {
    "can_mark_seen": boolean,
    "can_create": boolean,
    "can_delete": boolean,
    "can_read": boolean,
    "can_rename": boolean,
    "can_edit": boolean,
    "can_subscribe": boolean
  },
  "collapsed": boolean,
  "default": boolean,
  "subscribed": boolean,
  "favorited": boolean,
  "favorite": {
    "nick_name": string,
    "order": number
  },
  "local": boolean,
  "public": boolean,
  "shared": boolean,
  "type": number,
  "counts": {
    "flagged": number,
    "messages": number,
    "new": number,
    "unread": number,
    "snoozed": number
  },
  "trash": boolean,
  "sent": boolean,
  "drafts": boolean,
  "owner": string,
  "has_external_calendars": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Folder ID |
| `display_name` | string | Folder name (not the full path) |
| `path` | string | Full path |
| `parent_id` | number | Parent folder ID |
| `depth` | number | 0 for root folders |
| `has_subs` | boolean | Has sub directories |
| `admin` | boolean | User has admin permissions |
| `has_new` | boolean | Folder has new items |
| `permissions` | object | Only output when true |
| `collapsed` | boolean | Folder is collapsed in UI |
| `default` | boolean | Is a default folder and cannot be edited |
| `subscribed` | boolean | User is subscribed to folder |
| `favorited` | boolean | Folder is favorited |
| `favorite` | object | Output if favorited is true |
| `local` | boolean | Is a local folder |
| `public` | boolean | Is a public folder |
| `shared` | boolean | Is a shared folder |
| `type` | number | 0 Email, 1 Calendar, 2 Contacts, 3 Tasks, 4 Notes, 5 Documents |
| `counts` | object | Output if type == 0 (Email) |
| `trash` | boolean | Output if folder is the Deleted Items |
| `sent` | boolean | Output if folder is the Sent Items |
| `drafts` | boolean | Output if folder is Drafts |
| `owner` | string | Folder owner email address |
| `has_external_calendars` | boolean | Only output for calendars that download events from external ics links |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "id"
}
```

---

### All Folders

#### GET `/WorldClientAPI/folders`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `load_unsubscribed` | number | No | Set to `1` to reload folders on server when `HideUnsubscribedFolders=Yes`. Only call once per session. Used to subscribe/unsubscribe from folders. |

**Example Request**

```http
GET /WorldClientAPI?route=folders&session={session_id}
```

```http
GET /WorldClientAPI/folders?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "folders": []
}
```

| Field | Type | Description |
|-------|------|-------------|
| `folders` | array | Array of folder objects |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "route"
}
```

---

### All Folders of Type

#### GET `/WorldClientAPI/folders/type/{type}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | number | Yes | 0 Email, 1 Calendar, 2 Contacts, 3 Tasks, 4 Notes, 5 Documents |

**Example Request**

```http
GET /WorldClientAPI?route=folders&type={type}&session={session_id}
```

```http
GET /WorldClientAPI/folders/type/{type}?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "folders": []
}
```

| Field | Type | Description |
|-------|------|-------------|
| `folders` | array | Array of folder objects of the type specified |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "type"
}
```

---

### Get Notification Folders

List of mail folders that the user wishes to be notified regarding new messages. This feature is available when `EnableDesktopNotificationsForEmail` is enabled and `DesktopNotificationsFolders` is set to `Selected`.

#### GET `/WorldClientAPI/folders/notifications`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `notifications` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?route=folders&session={session_id}&notifications=1
```

```http
GET /WorldClientAPI/folders/notifications?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "notify_folders": ["Inbox", "Inbox/OtherFolder"]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `notify_folders` | array | Array of folder paths |

**Error Responses**

| Status | Description |
|--------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |

---

### Get Access Control List

List of users with access control and their permission levels.

The `access` variable is a number that represents the access control level for a user:

| Level | Value |
|-------|-------|
| admin | 511 |
| edit | 191 |
| read | 3 |
| none | 0 |

The user can be `anyone`, a local email address, or an MDaemon Group. Groups are represented by `group:%NAME%`.

#### GET `/WorldClientAPI/folders/{folder_id}?acl=1`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `acl` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?route=folders&session={session_id}&id={folder_id}&acl=1
```

```http
GET /WorldClientAPI/folders/{folder_id}?session={session_id}&acl=1
```

**Success Response** — `200 OK`

```json
{
  "acl": [
    {
      "user": "anyone",
      "access": 0,
      "read_only_user": true
    },
    {
      "user": "folder_owner@mail.test",
      "access": 511,
      "read_only_user": true,
      "read_only_access": true
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `acl` | array | Array of user ACL objects |
| `acl[].user` | string | User email or `anyone` or `group:%NAME%` |
| `acl[].access` | number | Access level (cannot be admin for `anyone`) |
| `acl[].read_only_user` | boolean | The user cannot be changed |
| `acl[].read_only_access` | boolean | The access level cannot be changed |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidParameter` | Invalid `id` parameter |
| 401 | Unauthorized | Authentication required |
| 404 | Not Found | Folder not found |

---

## POST Requests

### Rename

#### POST `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `rename` | string | Yes | New folder name |

**Example Request**

```http
POST /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "rename": "{new_name}"
}
```

```http
POST /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "rename": "{new_name}"
}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "id": number
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

### Move

#### POST `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `move` | number | Yes | New parent folder ID. Use `-1` to move to root. |

**Example Request**

```http
POST /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "move": {new_parent_id}
}
```

```http
POST /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "move": {new_parent_id}
}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "id": number
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

### Mark All Read

This request will only work on a message folder.

#### POST `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `markallread` | boolean | Yes | Set to `true` |

**Example Request**

```http
POST /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "markallread": true
}
```

```http
POST /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "markallread": true
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

```json
{
  "error": "InvalidParameter",
  "parameter": "markallread"
}
```

```json
{
  "error": "InvalidParameter",
  "parameter": "id"
}
```

---

## PUT Requests

### Create Folder

#### PUT `/WorldClientAPI/folders`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `create` | number | Yes | Must be `1` |
| `name` | string | Yes | Folder name |
| `type` | number | Yes | 0 Email, 1 Calendar, 2 Contacts, 3 Tasks, 4 Notes, 5 Documents |
| `parent_id` | number | Yes | Parent folder ID |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "create": 1,
  "type": {type},
  "name": "{name}",
  "parent_id": {parent_id}
}
```

```http
PUT /WorldClientAPI/folders?session={session_id}
Content-Type: application/json

{
  "create": 1,
  "type": {type},
  "name": "{name}",
  "parent_id": {parent_id}
}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "id": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | The new folder ID |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "name"
}
```

---

### Favorite Toggle

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `favorite` | boolean | Yes | `true` to favorite, `false` to unfavorite |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "favorite": true
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "favorite": true
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "favorite"
}
```

---

### Favorite Nickname

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `nick_name` | string | Yes | Nickname for the favorite folder |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "nick_name": "{nickname}"
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "nick_name": "{nickname}"
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "nick_name"
}
```

---

### Favorite Order

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `order` | number | Yes | Display order for the favorite folder |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "order": {order}
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "order": {order}
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "order"
}
```

---

### Subscribe Toggle

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `subscribe` | boolean | Yes | `true` to subscribe, `false` to unsubscribe |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "subscribe": true
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "subscribe": true
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "subscribe"
}
```

---

### Collapse Toggle

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `collapse` | boolean | Yes | `true` to collapse, `false` to expand |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "collapse": true
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "collapse": true
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "collapse"
}
```

---

### Checked Calendar Toggle

When `checked` is true, the calendar folder is selected for viewing. This property is remembered on the server since you can have more than one calendar's events displayed on the client at once.

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `checked` | boolean | Yes | `true` to select calendar for viewing, `false` to deselect |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "id": {folder_id},
  "checked": true
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "checked": true
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "checked"
}
```

---

### Put Notification Folders

List of mail folders that the user wishes to be notified regarding new messages. This feature is available when `EnableDesktopNotificationsForEmail` is enabled and `DesktopNotificationsFolders` is set to `Selected`.

#### PUT `/WorldClientAPI/folders`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `notifications` | array | Yes | Array of folder paths to be notified |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}
Content-Type: application/json

{
  "notifications": ["Inbox", "OtherFolder"]
}
```

```http
PUT /WorldClientAPI/folders?session={session_id}
Content-Type: application/json

{
  "notifications": ["Inbox", "OtherFolder"]
}
```

> No need to send `"TestFolder": false` since undefined is falsy.

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Description |
|--------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |

---

### Put Access Control List

Set the list of users and the access level for a specific folder.

The `access` variable is a number that represents the access control level for a user:

| Level | Value |
|-------|-------|
| admin | 511 |
| edit | 191 |
| read | 3 |
| none | 0 |

The user can be `anyone`, a local email address, or an MDaemon Group. Groups are represented by `group:%NAME%`.

#### PUT `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `acl` | array | Yes | Array of user ACL objects |
| `apply_to_subs` | number | No | Set to `1` to apply settings to all sub folders |

**Example Request**

```http
PUT /WorldClientAPI?route=folders&session={session_id}&id={folder_id}
Content-Type: application/json

{
  "acl": [
    {
      "user": "anyone",
      "access": 0
    },
    {
      "user": "folder_owner@mail.test"
    },
    {
      "user": "my_boss@mail.test",
      "access": 191
    }
  ],
  "apply_to_subs": 1
}
```

```http
PUT /WorldClientAPI/folders/{folder_id}?session={session_id}
Content-Type: application/json

{
  "acl": [
    {
      "user": "anyone",
      "access": 0
    },
    {
      "user": "folder_owner@mail.test"
    },
    {
      "user": "my_boss@mail.test",
      "access": 191
    }
  ],
  "apply_to_subs": 1
}
```

> If the first two ACL entries (`anyone` and folder owner) are not included, they will be set to defaults. The `anyone` user's access cannot be set to admin level.

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidParameter` | Invalid `id` parameter |
| 401 | Unauthorized | Authentication required |
| 404 | Not Found | Folder not found |

---

## DELETE Requests

### Delete Folder

#### DELETE `/WorldClientAPI/folders/{folder_id}`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |

**Example Request**

```http
DELETE /WorldClientAPI?route=folders&id={folder_id}&session={session_id}
```

```http
DELETE /WorldClientAPI/folders/{folder_id}?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "{error_message}"
}
```

---

### Empty Deleted Items Folder

Empty the Deleted Items Folder of all messages.

#### DELETE `/WorldClientAPI/folders/empty`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `empty` | number | Yes | Must be `1` |

**Example Request**

```http
DELETE /WorldClientAPI?route=folders&empty=1&session={session_id}
```

```http
DELETE /WorldClientAPI/folders/empty?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Response**

```json
{
  "error": "{error_message}"
}
```

---

### Delete All Messages

This request will only work on a message folder.

#### DELETE `/WorldClientAPI/folders/{folder_id}/deleteall`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Folder ID |
| `deleteall` | number | Yes | Must be `1` |

**Example Request**

```http
DELETE /WorldClientAPI?route=folders&id={folder_id}&deleteall=1&session={session_id}
```

```http
DELETE /WorldClientAPI/folders/{folder_id}/deleteall?session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

```json
{
  "error": "InvalidParameter",
  "parameter": "deleteall"
}
```

```json
{
  "error": "InvalidParameter",
  "parameter": "id"
}
```

---

## Additional Errors

| Error | Description |
|-------|-------------|
| `InvalidParameter` | The parameter value had an invalid value |
| `InadequatePermissions` | The attempted action is not permitted on this folder for this user |

---

## Important Notes

### Alternative POST/PUT Format

All POSTs and PUTs can use base64-encoded JSON:

```http
POST /WorldClientAPI?route=folders&session={session_id}&request={base64_encoded_json}
```

Example JSON before encoding (Favorite Nickname):

```json
{
  "id": {folder_id},
  "nick_name": "{nick_name}"
}
```

Encoded: `request=eyJpZCI6e2ZvbGRlcklkfSwibmlja05hbWUiOntuaWNrTmFtZX19`

> The `request` parameter can be part of form data. Be sure to URI encode it.
