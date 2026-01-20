# WorldClient API

> MDaemon Webmail's JSON API for building web applications that interact with email, calendars, contacts, and more.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Overview](#overview)
- [Configuration](#configuration)
- [Request Formats](#request-formats)
- [Authentication](#authentication)
- [Routes](#routes)
- [Resources](#resources)
- [Changelog](#changelog)

---

## Overview

This is the documentation for MDaemon Webmail's JavaScript Object Notation (JSON) API.

## Configuration

The API is enabled by default in `WorldClient\Domains.ini` under `[Default:Settings]`:

```ini
EnableWorldClientAPI=Yes
```

### CORS Configuration

To allow access from external web applications, add the following to `Domains.ini` in the `[%DOMAIN%:Settings]` section:

**Allow all external origins:**

```ini
WorldClientAPI:AllowAllExternalOrigins=Yes
```

**Allow specific domains:**

```ini
WorldClientAPI:AllowTheseExternalOrigins=https://www.example.com;https://mail.example2.com
```

Alternatively, set the `Access-Control-Allow-Origin` HTTP Response Header in Remote Administration at **Main > Webmail Settings > Web Server**. Set it to `*` for any external origin or specify a single origin (the header does not support multiple origins).

---

## Request Formats

All `POST` and `PUT` requests accept data in the following formats:

| Format | Content-Type |
|--------|--------------|
| Form data | `application/x-www-form-urlencoded` |
| JSON | `application/json` |
| Base64 JSON | Query parameter |

### Base64 Encoded JSON Example

```http
POST /WorldClientAPI?route=folders&session={session_id}&request={base64_encoded_json}
```

> The `request` parameter can also be included in form data. Be sure to URI encode it.

---

## Authentication

All API access requires authentication. See the [authenticate](authenticate.md) route for details.

### Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | The API route to access |
| `session` | string | Session ID from authentication |

### Request Examples

**Query parameter style:**

```http
GET /WorldClientAPI?route={route}&session={session_id}
```

**Path style:**

```http
GET /WorldClientAPI/{route}?session={session_id}
```

### Authorization Header

To avoid exposing the session ID in every request, pass it in the `Authorization` header:

```http
Authorization: Bearer {session_id}
```

**Fetch API example:**

```javascript
fetch("/WorldClientAPI/messages/1", {
  method: "GET",
  headers: {
    "Authorization": "Bearer {session_id}"
  }
});
```

---

## Routes

| Route | Description |
|-------|-------------|
| [authenticate](authenticate.md) | Session management, 2FA, password recovery, WebAuthn |
| [settings](settings.md) | User preferences, app passwords, IMAP filters, autoresponder |
| [folders](folders.md) | Folder operations, notifications, ACL |
| [compose](compose.md) | Email composition, signatures, templates |
| [messages](messages.md) | Message CRUD, threading, search, import/export |
| [contacts](contacts.md) | Contact management, vCard, AutoComplete |
| [events](events.md) | Calendar events, iCal support, external calendars |
| [tasks](tasks.md) | Task management |
| [notes](notes.md) | Notes management |
| [documents](documents.md) | Document storage |
| [rtc](rtc.md) | Real-time communication |
| [categories](categories.md) | Category management |
| [reminders](reminders.md) | Reminder management |
| [recorder](recorder.md) | Audio recording |
| [freebusy](freebusy.md) | Free/busy information |
| [cloud-storage](cloud-storage.md) | Dropbox, Google Drive, OneDrive integration |
| [conversations](conversations.md) | Chat conversation history |
| [ping](ping.md) | Keep-alive endpoint |

---

## Resources

| Resource | Description |
|----------|-------------|
| [Translations](translations.md) | Language support |
| [Uploads](uploads.md) | File upload handling |
| [ISO-8601 Wiki](https://en.wikipedia.org/wiki/ISO_8601) | Date format reference |

---

## Changelog

### Version 3.5

- Added cloud storage integration routes (Dropbox, Google Drive, OneDrive) — [cloud-storage](cloud-storage.md)
- Added conversation history storage — [conversations](conversations.md)
- Added ping keep-alive endpoint — [ping](ping.md)

### Version 3.4

- Added the `rtc` route for real-time communication features — [rtc](rtc.md)

### Version 3.3

- Added `deferred_date` to message properties — [messages](messages.md#single-message)
- Added `hyper_links_disabled` to message properties — [messages](messages.md#single-message)
- Added support for attaching message attachments to new events — [events](events.md#attach-from-message)
- Added support for attaching message attachments to new tasks — [tasks](tasks.md#attach-from-message)
- Added support for attaching message attachments to new notes — [notes](notes.md#attach-from-message)

### Version 3.2

- Added `replied_date` and `forwarded_date` to `message.status` — [messages](messages.md#single-message)
- Added persistent reminders, mark as seen/unseen, clear old reminders — [reminders](reminders.md)

### Version 3.1

- Added support for loading unsubscribed folders on request when `HideUnsubscribedFolders` is enabled — [folders](folders.md#all-folders)

### Version 3.0

- Added WebAuthn support — [authenticate](authenticate.md#credentials-api)
- **BREAKING:** Separated `otp_email` verification from 2FA authenticator app — [authenticate](authenticate.md#email-verification-setup), [settings](settings.md#2fa-email-verification-setup)
- **BREAKING:** Password recovery uses WorldClient Templates — [authenticate](authenticate.md#password-recovery)
- Added delete all attachments on a message — [messages](messages.md#delete-all-attachments)
- Added `description` column for Documents — [documents](documents.md#description)

### Version 2.6

- Added `use_oauth` in MultiPOP accounts — [settings](settings.md#pop-accounts)
- Added `send_read_confirm` for read messages — [messages](messages.md#send-read-confirm)
- Added publishing schedules on events route — [events](events.md#publish-schedule)

### Version 2.5

- Added Alias Display Name management — [settings](settings.md#add-alias-display-name)
- Added Next Unread message — [messages](messages.md#next-unread)
- Added `login_token` for CSRF protection — [authenticate](authenticate.md#basic-authentication)
- Added 2FA verification code via email — [authenticate](authenticate.md#two-factor-authentication-setup), [settings](settings.md#2fa-setup)
- Added verification code email management — [settings](settings.md#verification-code-email)
- Added email verification code request — [authenticate](authenticate.md#two-factor-authentication)

### Version 2.4.1

- Fixed `send_invite` option for meeting attendees — [events](events.md#single-event)

### Version 2.4

- Added App Passwords — [settings](settings.md#app-passwords)
- Added HTML support in Events — [events](events.md#single-event)
- Added HTML support in Contacts — [contacts](contacts.md#single-contact)
- Added HTML support in Tasks — [tasks](tasks.md#single-task)
- Added HTML support in Notes — [notes](notes.md#single-note)
- Added Out Of Office status retrieval — [contacts](contacts.md#out-of-office)
- Added `tracking_url` in message JSON — [messages](messages.md#single-message)
- Added List-Unsubscribe headers support — [messages](messages.md#single-message)
- Added message import — [messages](messages.md#import-messages)
- **MAJOR:** Added CSRF token support — [authenticate](authenticate.md#csrf-token)
- Added rename recorded tracks — [recorder](recorder.md#rename)

### Version 2.3.1

- Fixed multiple IDs for Move, Copy, Delete operations — [messages](messages.md#move)

### Version 2.3

- Added Message Threading — [messages](messages.md#threaded)
- Added thread expand/collapse — [messages](messages.md#expand-thread), [messages](messages.md#collapse-thread)
- Added message quotas — [messages](messages.md#quotas)
- Added `attach_event` to compose — [compose](compose.md#send-ical)
- Added `recorder` route — [recorder](recorder.md)
- Added `freebusy` route — [freebusy](freebusy.md)
- Removed support for new accounts
- Added default view for published calendars — [events](events.md#publish-calendar)
- Added free/busy link for published calendars — [events](events.md#publish-calendar)

### Version 2.2

- Added `search_all` and `search_subs` on message folders — [messages](messages.md#search)
- Added `saved_search` on message folders — [messages](messages.md#saved-searches)
- Added `common_contacts` — [contacts](contacts.md#common-contacts)
- Added `folder` property to AutoComplete response — [contacts](contacts.md#autocomplete)

### Version 2.1

- Added `markallread` on message folders — [folders](folders.md#mark-all-read)
- Added `deleteall` on message folders — [folders](folders.md#delete-all)
- Added Empty Trash documentation — [folders](folders.md#empty-trash)
- Added folder ACL support — [folders](folders.md#get-acl), [folders](folders.md#put-acl)

### Version 2.0

- API enabled by default for latest Webmail theme
- Added `application/json` support in POST/PUT requests
- Added path routing with examples
- Added unauthenticated `logon_settings` GET request
- Improved CORS support
- Added Authorization header support
- Added Disable Two Factor Authentication
- Added Password Recovery
- Added New Account actions (when enabled)
- Updated Change Password to require authenticated token
- Added Remember Me at `authenticate=basic` (HTTPS only)
- Added Remember Me at `authenticate=2fa` (HTTPS only)
- Changed some error responses from 200 to 400/401/403
- Added calendar folder `checked` state in folders route
- Fixed `POST snoozed` → `POST snooze` in messages route
- Changed `snoozed` status to only return when message is snoozed
- Deprecated `GET download={attachment_id}` → use `GET attachment={attachment_id}`
- Deprecated `POST upload=1` → use `POST attachment=1`
- Deprecated `POST upload_picture=1` → use `POST picture=1`
- Added vCard export — [contacts](contacts.md#export-vcf)
- Improved file upload documentation — [uploads](uploads.md)
- Fixed Documents download documentation — [documents](documents.md#download)
- Fixed `create`/`update` requirement for PUT on events
- Fixed occurrence editing documentation and server issue
- Added external calendar link management — [events](events.md)
- Added ICS import/export — [events](events.md)
- Added private/public calendar access links — [events](events.md)
- Fixed `can_edit_signature` → `can_edit_default_signature` in compose docs
- Attachment `size` now in bytes instead of kilobytes
- Added email templates — [compose](compose.md#templates)
- Added login history — [settings](settings.md#login-history)
- Added notification folders — [folders](folders.md#get-notify)
- Added IMAP filters — [settings](settings.md#imap-filters)
- Added POP accounts — [settings](settings.md#pop-accounts)
- Added AutoResponder — [settings](settings.md#autoresponder)
- Changed `type` → `mime_type` for message attachments
- Added single occurrence retrieval — [events](events.md#single-event)
