# Uploads

> File upload handling for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Form Setup](#form-setup)
- [PIM Attachments](#pim-attachments)
- [Inline Compose Attachments](#inline-compose-attachments)

---

## Form Setup

Some upload routes allow for multiple attachments. The example below uses the `documents` route, but other routes are available.

```html
<form enctype="multipart/form-data" method="post" multiple="multiple" action="/WorldClientAPI/documents/{folder_id}">
  <input type="file" name="Attachment" />
</form>
```

---

## PIM Attachments

Each type of PIM (Personal Information Management) item allows the addition of attachments. This includes:

- `events`
- `contacts`
- `tasks`
- `notes`

Uploaded PIM attachments accept multiple files at once.

**XMLHttpRequest Example**

If using an `XMLHttpRequest` to upload files, the file name should be prefixed with `Attachment`:

```javascript
let data = new FormData();
files.forEach(file => {
  data.append("Attachment", file);
});

let xhr = new XMLHttpRequest();

// ... configure xhr ...

// For existing contact
xhr.open("POST", "/WorldClientAPI/contacts/personal/contact/{guid}/attachment");
xhr.send(data);
```

**For New Items**

```javascript
xhr.open("POST", "/WorldClientAPI/contacts/personal/new/attachment");
xhr.send(data);
```

---

## Inline Compose Attachments

Uploading attachments to the `compose` route is the same as in [PIM Attachments](#pim-attachments) except when it comes to inline attachments.

**Important:** When uploading an inline attachment, the form input name must be `upload` (not `Attachment`).

```javascript
let data = new FormData();
files.forEach(file => {
  data.append("upload", file); // Note: "upload" instead of "Attachment"
});

let xhr = new XMLHttpRequest();

// ... configure xhr ...

xhr.open("POST", "/WorldClientAPI/compose/{compose_id}/inline");
xhr.send(data);
```
