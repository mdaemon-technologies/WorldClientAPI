# Uploads

> File upload handling for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Form Setup](#form-setup)
- [PIM Attachments](#pim-attachments)
- [Compose Attachments](#compose-attachments)

---

## Form Setup

Some upload routes allow for multiple attachments. The example below uses the `documents` route, but other routes are available.

```html
<form enctype="multipart/form-data" method="post" multiple="multiple" action="/WorldClientAPI/documents/{folder_id}">
  <input type="file" name="Attachment" />
</form>
```

Each file is one part of a `multipart/form-data` request body:

- The part's form field name begins with `Attachment` (for example `Attachment` or `Attachment:0`), except where a route says otherwise. `Attachments` is not a file field.
- The part has a non-empty `filename`. A part without one is ignored. Any path in the filename is removed.
- In the part's `Content-Disposition` header, `name` comes before `filename`, as browsers and HTTP libraries write it.
- The part's `Content-Type` is the file's MIME type.

---

## PIM Attachments

Each type of PIM (Personal Information Management) item allows the addition of attachments. This includes:

- `events`
- `contacts`
- `tasks`
- `notes`

Uploaded PIM attachments accept multiple files at once.

**XMLHttpRequest Example**

If using an `XMLHttpRequest` to upload files, the form field name must begin with `Attachment`:

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

## Compose Attachments

Uploading attachments to a message being composed is the same as in [PIM Attachments](#pim-attachments): the form field name must begin with `Attachment`. A file sent under any other field name is not attached; check the `attachments` list in the response.

```javascript
let data = new FormData();
files.forEach((file, i) => {
  data.append(`Attachment:${i}`, file);
});

let xhr = new XMLHttpRequest();

// ... configure xhr ...

xhr.open("POST", "/WorldClientAPI/compose/{compose_id}/attachment");
xhr.send(data);
```

**Inline Images**

An inline image is the exception: its form field name must be `file` or `upload` (lower case, not `Attachment`), and it goes to the `attachment/inline` route. Send one image per request; the response carries its URL.

```javascript
let data = new FormData();
data.append("file", image); // Note: "file" or "upload" instead of "Attachment"

let xhr = new XMLHttpRequest();

// ... configure xhr ...

xhr.open("POST", "/WorldClientAPI/compose/{compose_id}/attachment/inline");
xhr.send(data);
```

See [Upload Attachment](compose.md#upload-attachment) and [Upload Inline Image](compose.md#upload-inline-image) for the responses.
