[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

# Compose

## Requests

### GET
- [New Message](#new-message)
- [Reply to Message](#reply-to-message)
- [Reply All to Message](#reply-all-to-message)
- [Forward Message](#forward-message)
- [Forward Message as Attachment](#forward-message-as-attachment)
- [Open Draft](#open-draft)
- [Attach Document](#attach-document)
- [Signatures](#signatures)
- [Email Templates](#email-templates)
- [Download Attachment](#download-attachment)
- [Send Contact vCard](#send-contact-vcard)
- [Send Event iCal](#send-event-ical)
- [Send Recording](#send-recording)

### POST
- [Send Message](#send-message)
- [Upload Attachment](#upload-attachment)
- [Upload Inline Image](#upload-inline-image)
- [Upload Signature Inline Image](#upload-signature-inline-image)

### PUT
- [Save Draft](#save-draft)
- [Update Signatures](#update-signatures)
- [Update Email Templates](#update-email-templates)

### DELETE
- [Delete Attachment](#delete-attachment)
- [Delete Draft](#delete-draft)

---

## Required URI Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| route | string | `compose` |
| session | string | `{session_id}` |

---

## GET

### New Message

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |

**Success Response**

```json
{
    "id": number,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1

GET /WorldClientAPI/compose/new?session={session_id}
```

---

### Reply to Message

The value of `reply` is the id of the message you are replying to.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| reply | number | `{message_id}` |
| folder | number | `{folder_id}` |

**Success Response**

```json
{
    "id": number,
    "to": string,
    "subject": string,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| to | string | The reply-to address header of the message being replied to |
| subject | string | Subject line |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | The previous message designated by reply and a selected signature will be included here |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={folder_id}&reply={message_id}

GET /WorldClientAPI/compose/new/reply/{folder_id}/{message_id}?session={session_id}
```

---

### Reply All to Message

The value of `reply_all` is the id of the message you are replying to.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| reply_all | number | `{message_id}` |
| folder | number | `{folder_id}` |

**Success Response**

```json
{
    "id": number,
    "to": string,
    "cc": string,
    "subject": string,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| to | string | The comma delimited list of from, to, and cc address headers excluding the user's address |
| cc | string | Only present if there were addresses in the cc of the message being replied to |
| subject | string | Subject line |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | The previous message designated by reply and a selected signature will be included here |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={folder_id}&reply_all={message_id}

GET /WorldClientAPI/compose/new/reply_all/{folder_id}/{message_id}?session={session_id}
```

---

### Forward Message

The value of `forward` is the id of the message you are forwarding.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| forward | number | `{message_id}` |
| folder | number | `{folder_id}` |

**Success Response**

```json
{
    "id": number,
    "subject": string,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| subject | string | Subject line |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | The previous message designated by forward and a selected signature will be included here |
| attachments | array | Attachments that are part of the forwarded message |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={folder_id}&forward={message_id}

GET /WorldClientAPI/compose/new/forward/{folder_id}/{message_id}?session={session_id}
```

---

### Forward Message as Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| forward | number | `{message_id}` - comma delimited list of emails to attach |
| folder | number | `{folder_id}` |
| attachment | number | `1` |

**Success Response**

```json
{
    "id": number,
    "subject": string,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "body": string,
    "max_attachment_size": number,
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| subject | string | Subject line |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| body | string | A selected signature will be included here |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| attachments | array | The attached messages with extension .eml |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&attachment=1&folder={folder_id}&forward={message_id1,message_id2,etc.}

GET /WorldClientAPI/compose/new/forward_attachment/{folder_id}/{message_id}?session={session_id}

GET /WorldClientAPI/compose/new/forward_attachment/{folder_id}/ids?session={session_id}&forward={message_id1,message_id2,etc.}
```

---

### Open Draft

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| draft | number | The id of the message in the Drafts folder |

**Success Response**

```json
{
    "id": number,
    "to": string,
    "cc": string,
    "bcc": string,
    "reply_to": string,
    "subject": string,
    "deferred_delivery": string,
    "confirm_delivery": boolean,
    "confirm_read": boolean,
    "urgent": boolean,
    "from": number,
    "from_addresses": [
        string,
        string
    ],
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | `{compose_id}` |
| to | string | Comma delimited list of RFC 2822 formatted email addresses |
| cc | string | Comma delimited list of RFC 2822 formatted email addresses |
| bcc | string | Comma delimited list of RFC 2822 formatted email addresses |
| reply_to | string | RFC 2822 formatted email address |
| subject | string | Subject line |
| deferred_delivery | string | ISO-8601 Date YYYY-MM-DDTHH:mm+/-HH:mm |
| confirm_delivery | boolean | Request delivery confirmation |
| confirm_read | boolean | Request read confirmation |
| urgent | boolean | Mark as urgent |
| from | number | Index of from option chosen by user |
| from_addresses | string[] | Primary address plus aliases user can send from |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| attachments | array | List of attachments |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": "InvalidParameter",
    "parameter": "draft"
}
```

The message is not a draft message.

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&draft={message_id}

GET /WorldClientAPI/compose/draft/{message_id}?session={session_id}
```

---

### Attach Document

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` (or use `id` instead) |
| id | number | `{compose_id}` (or use `new` instead) |
| folder | number | The id of the documents folder where the documents are located |
| attach_document | number | `{document_id}` - comma delimited list of documents to attach |

**Success Response**

```json
{
    "id": number,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Compose ID |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| attachments | array | List of attachments |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": "InvalidParameter",
    "parameter": "folder"
}
```

The folder id requested was not a documents folder, or `parameter: "attach_document"` if no such document id(s).

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={documents_folder_id}&attach_document={document_id1,document_id2,etc.}

GET /WorldClientAPI?session={session_id}&route=compose&id=1&folder={documents_folder_id}&attach_document={document_id1,document_id2,etc.}

GET /WorldClientAPI/compose/new/attach_document/{documents_folder_id}/{document_id}?session={session_id}

GET /WorldClientAPI/compose/{compose_id}/attach_document/{documents_folder_id}/{document_id}?session={session_id}

GET /WorldClientAPI/compose/new/attach_document/{documents_folder_id}/ids?session={session_id}&attach_document={document_id1,document_id2,etc.}

GET /WorldClientAPI/compose/{compose_id}/attach_document/{documents_folder_id}/ids?session={session_id}&attach_document={document_id1,document_id2,etc.}
```

---

### Signatures

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| signatures | number | `1` |
| id | number | `{compose_id}` - Required for getting signatures in a compose window. Not required for simply retrieving signatures for viewing. |

**Success Response**

```json
{
    "success": true,
    "signatures": [
        {
            "id": string,
            "defaultAddresses": [string],
            "name": string,
            "html": string,
            "text": string
        }
    ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Operation success status |
| signatures | array | List of signatures |
| signatures[].id | string | Signature ID |
| signatures[].defaultAddresses | string[] | Addresses that this signature is applied to |
| signatures[].name | string | Signature name |
| signatures[].html | string | HTML content |
| signatures[].text | string | Plain text content |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&signature=1&id={compose_id}?session={session_id}

GET /WorldClientAPI/compose/{compose_id}/signatures?session={session_id}

# For viewing only
GET /WorldClientAPI/compose/0/signatures?session={session_id}
```

---

### Email Templates

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| templates | number | `1` |
| id | number | `{compose_id}` - Required for getting templates in a compose window. Not required for simply retrieving templates for viewing. |

**Success Response**

```json
{
    "templates": [
        {
            "id": number,
            "image": string,
            "title": string,
            "description": string,
            "html": string
        }
    ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| templates | array | List of templates |
| templates[].id | number | Template ID |
| templates[].image | string | Template image |
| templates[].title | string | Template title |
| templates[].description | string | Template description |
| templates[].html | string | HTML content |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&templates=1&id={compose_id}?session={session_id}

GET /WorldClientAPI/compose/{compose_id}/templates?session={session_id}

# For viewing only
GET /WorldClientAPI/compose/0/templates?session={session_id}
```

---

### Download Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | `{compose_id}` |
| attachment | number | `{attachment_id}` |
| download | number | `{attachment_id}` (deprecated) |

**Success Response**

Download begins.

**Failure Response**

- `404 Not Found`
- `500 Internal Server Error`

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&id={compose_id}&attachment={attachment_id}

GET /WorldClientAPI/compose/{compose_id}/attachment/{attachment_id}?session={session_id}
```

---

### Send Contact vCard

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| folder | number | `{contact_folder_id}` |
| attach_contact | string | `{contact_id}` - CSV list of contact_ids |

**Success Response**

```json
{
    "id": number,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": "text/vcard",
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Compose ID |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| attachments | array | List of vCard attachments |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | `text/vcard` |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": "InvalidParameter",
    "parameter": "folder"
}
```

The folder id requested was not a contacts folder, or `parameter: "contact"` if no such contact id(s).

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={contacts_folder_id}&attach_contact={contact_id1,contact_id2,etc.}

GET /WorldClientAPI/compose/new/attach_contact/{contact_folder_id}/{contact_id}?session={session_id}

GET /WorldClientAPI/compose/new/attach_contact/{contact_folder_id}/ids?session={session_id}&id={contact_id1,contact_id2,etc.}
```

---

### Send Event iCal

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| folder | number | `{calendar_folder_id}` |
| attach_event | number | `{event_id}` - only one allowed |

**Success Response**

```json
{
    "id": number,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": "ical.ics",
            "size": number,
            "mime_type": "text/calendar",
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Compose ID |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| attachments | array | List of iCal attachments |
| attachments[].name | string | `ical.ics` |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | `text/calendar` |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": "InvalidParameter",
    "parameter": "folder"
}
```

The folder id requested was not a calendar folder, or an empty error if no such event id.

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={calendar_folder_id}&attach_event={event_id}

GET /WorldClientAPI/compose/new/attach_event/{calendar_folder_id}/{event_id}?session={session_id}
```

---

### Send Recording

If you have a voice recording from the `record` route, you can attach it to a new message.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| new | number | `1` |
| attach_audio | string | `{filename}` - only one allowed |

**Success Response**

```json
{
    "id": number,
    "from_addresses": [
        string,
        string
    ],
    "reply_to": string,
    "max_attachment_size": number,
    "body": string,
    "attachments": [
        {
            "name": "{filename}",
            "size": number,
            "mime_type": "audio/mpeg",
            "id": number
        }
    ],
    "permissions": {
        "can_edit_default_signature": boolean,
        "can_defer_delivery": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Compose ID |
| from_addresses | string[] | Primary address plus aliases user can send from |
| reply_to | string | Default Reply-To address |
| max_attachment_size | number | Maximum size of an attachment in bytes |
| body | string | A selected signature will be included here |
| attachments | array | List of audio attachments |
| attachments[].name | string | The filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | `audio/mpeg` |
| attachments[].id | number | Attachment ID |
| permissions.can_edit_default_signature | boolean | Whether user can edit default signature |
| permissions.can_defer_delivery | boolean | Whether user can defer delivery |

**Failure Response**

```json
{
    "error": "InvalidParameter",
    "parameter": "folder"
}
```

The folder id requested was not a calendar folder, or an empty error if no such event id.

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=compose&new=1&folder={calendar_folder_id}&attach_audio={filename}

GET /WorldClientAPI/compose/new/attach_audio/{calendar_folder_id}/{filename}?session={session_id}
```

---

## POST

### Send Message

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| send | number | `1` |
| id | number | `{compose_id}` |
| to | string | Comma delimited list of RFC 2822 formatted email addresses |
| cc | string | Comma delimited list of RFC 2822 formatted email addresses |
| bcc | string | Comma delimited list of RFC 2822 formatted email addresses |
| from | number | Index of from option chosen by user |
| reply_to | string | RFC 2822 formatted email address |
| confirm_delivery | boolean | Request delivery confirmation |
| confirm_read | boolean | Request read confirmation |
| urgent | boolean | Mark as urgent |
| save_sent_message | boolean | User override for user setting SaveSentMail |
| deferred_delivery | string | ISO-8601 Date YYYY-MM-DDTHH:mm+/-HH:mm * |
| subject | string | Subject line |
| body | string | HTML string |

> **Note:** The Deferred Delivery feature must be enabled on the server in order for this value to be used. The date is when the message delivery should be delayed until. It will not impact the Date header of the message, which most clients use to describe when a message was sent.

**Success Response**

```json
{
    "success": true
}
```

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=compose&send=1&id={compose_id}

POST /WorldClientAPI/compose/{compose_id}/send?session={session_id}
```

---

### Upload Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | `{compose_id}` |
| attachment | number | `1` |
| upload | number | `1` (deprecated) |

**Success Response**

```json
{
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| attachments | array | List of attachments |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=compose&attachment=1&id={compose_id}

POST /WorldClientAPI/compose/{compose_id}/attachment?session={session_id}
```

---

### Upload Inline Image

If the attachment mime type is not an image, this request will return an error.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | `{compose_id}` |
| attachment | number | `1` |
| inline | number | `1` |
| upload_inline | number | `1` (deprecated) |

**Success Response**

```json
{
    "url": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| url | string | Temporary URL to image |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=compose&attachment=1&inline=1&id={compose_id}

POST /WorldClientAPI/compose/{compose_id}/attachment/inline?session={session_id}
```

---

### Upload Signature Inline Image

If the attachment mime type is not an image, this request will return an error.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| attachment | number | `1` |
| signature | number | `1` |

**Success Response**

```json
{
    "url": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| url | string | Temporary URL to image |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=compose&attachment=1&signature=1

POST /WorldClientAPI/compose/attachment/signature?session={session_id}
```

---

## PUT

### Save Draft

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| save | number | `1` |
| id | number | `{compose_id}` |
| to | string | Comma delimited list of RFC 2822 formatted email addresses |
| cc | string | Comma delimited list of RFC 2822 formatted email addresses |
| bcc | string | Comma delimited list of RFC 2822 formatted email addresses |
| from | number | Index of from option chosen by user |
| reply_to | string | RFC 2822 formatted email address |
| confirm_delivery | boolean | Request delivery confirmation |
| confirm_read | boolean | Request read confirmation |
| urgent | boolean | Mark as urgent |
| deferred_delivery | string | ISO-8601 Date YYYY-MM-DDTHH:mm+/-HH:mm |
| subject | string | Subject line |
| body | string | HTML string |

**Success Response**

```json
{
    "success": true
}
```

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=compose&save=1&id={compose_id}

PUT /WorldClientAPI/compose/{compose_id}/save?session={session_id}
```

---

### Update Signatures

**Signature Object**

```json
{
    "id": string,
    "defaultAddresses": [string],
    "name": string,
    "html": string,
    "text": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | string | 10 character max string of numbers and letters only |
| defaultAddresses | string[] | Addresses that this signature is applied to (equivalent to index of from_addresses) |
| name | string | Signature name |
| html | string | HTML content |
| text | string | Plain text content |

**Parameters**

None

**Success Response**

```json
{
    "success": true
}
```

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=compose

PUT /WorldClientAPI/compose/0?session={session_id}
```

**PUT Data**

```json
{
    "signatures": [{}, {}]
}
```

---

### Update Email Templates

**Template Object**

```json
{
    "id": number,
    "image": string,
    "title": string,
    "description": string,
    "html": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Template ID |
| image | string | Template image |
| title | string | Template title |
| description | string | Template description |
| html | string | HTML content |

**Parameters**

None

**Success Response**

```json
{
    "success": true
}
```

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=compose

PUT /WorldClientAPI/compose/0?session={session_id}
```

**PUT Data**

```json
{
    "templates": [{}, {}]
}
```

---

## DELETE

### Delete Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | `{compose_id}` |
| attachment | number | `{attachment_id}` |

**Success Response**

```json
{
    "attachments": [
        {
            "name": string,
            "size": number,
            "mime_type": string,
            "id": number
        }
    ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| attachments | array | Remaining list of attachments |
| attachments[].name | string | Attachment filename |
| attachments[].size | number | Size in bytes |
| attachments[].mime_type | string | MIME type |
| attachments[].id | number | Attachment ID |

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=compose&id={compose_id}&attachment={attachment_id}

DELETE /WorldClientAPI/compose/{compose_id}/attachment/{attachment_id}?session={session_id}
```

---

### Delete Draft

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| draft | number | `{compose_id}` |

**Success Response**

```json
{
    "success": true
}
```

**Failure Response**

```json
{
    "error": string
}
```

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=compose&draft={compose_id}

DELETE /WorldClientAPI/compose/{compose_id}/draft?session={session_id}
```

---

## Errors

| Error | Description |
|-------|-------------|
| InvalidParameter | The parameter value returned had an invalid value |
| InadequatePermissions | The attempted action is not permitted on this message for this user |
| InvalidRequest | There were invalid parameters in the request |
| MissingParameter | There were parameters missing from the request |
| InvalidAction | The requested action could not be completed on the item |
