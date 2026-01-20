# Contacts

Manage contacts including create, read, update, delete operations, vCard import/export, CSV import/export, contact groups, autocomplete, and company/department organization.

---

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Table of Contents

- [Required URI Parameters](#required-uri-parameters)
- [GET Requests](#get-requests)
  - [Single Contact](#single-contact)
  - [All Contacts](#all-contacts)
  - [Autocomplete Contacts](#autocomplete-contacts)
  - [Common Contacts](#common-contacts)
  - [Sort Contacts](#sort-contacts)
  - [Search Contacts](#search-contacts)
  - [Sort Group Members](#sort-group-members)
  - [Download Attachment](#download-attachment)
  - [Export Contact vCard](#export-contact-vcard)
  - [Export Contacts as CSV](#export-contacts-as-csv)
  - [Companies](#companies)
  - [Company Departments](#company-departments)
  - [Department Contacts](#department-contacts)
  - [Out of Office Status](#out-of-office-status)
  - [Contacts Pictures](#contacts-pictures)
- [POST Requests](#post-requests)
  - [Upload Attachment](#upload-attachment)
  - [Upload Picture](#upload-picture)
  - [Move](#move)
  - [Copy](#copy)
  - [Import Contact vCard](#import-contact-vcard)
  - [Import Contacts from CSV](#import-contacts-from-csv)
- [PUT Requests](#put-requests)
  - [Create Contact](#create-contact)
  - [Update Contact](#update-contact)
  - [Update Common Contacts](#update-common-contacts)
- [DELETE Requests](#delete-requests)
  - [Delete Contact](#delete-contact)
  - [Delete Attachment](#delete-attachment)
  - [Delete Picture](#delete-picture)
- [Errors](#errors)

---

## Required URI Parameters

```
route=contacts
folder={folder_id}
```

or

```
address_book={address_book_name or folder_id}
```

**address_book_name options:**

| Value | Description |
|-------|-------------|
| `personal` | Personal contacts |
| `full` | All contact folders |
| `global` | Global address book |
| `fax` | Fax contacts |

> **Note:** If requesting a single contact from the `full` address book, pass the folder id instead of `full`, because the `full` address book is all of the contact folders.

---

## GET Requests

### Single Contact

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID (GUID) |

**Response - Success**

```json
{
  "id": "string",
  "name": "string",
  "group": false,
  "emails": {
    "email": "string",
    "email2": "string",
    "email3": "string"
  },
  "name_details": {
    "first_name": "string",
    "middle_name": "string",
    "last_name": "string",
    "title": "string",
    "suffix": "string"
  },
  "categories": "string",
  "web_addresses": [
    {
      "type": "string",
      "address": "string"
    }
  ],
  "company": "string",
  "title": "string",
  "office": "string",
  "department": "string",
  "assistant": "string",
  "manager": "string",
  "addresses": [
    {
      "type": "string",
      "address": "string",
      "city": "string",
      "state": "string",
      "zipcode": "string",
      "country": "string"
    }
  ],
  "phone": {
    "home_mobile": "string",
    "mobile": "string",
    "business": "string",
    "business2": "string",
    "home": "string",
    "home2": "string",
    "business_main": "string",
    "assistant": "string",
    "business_fax": "string",
    "home_fax": "string",
    "car": "string",
    "pager": "string",
    "radio": "string",
    "ip": "string",
    "other": "string"
  },
  "nick_name": "string",
  "partner": "string",
  "children": "string",
  "birthday": "string",
  "anniversary": "string",
  "notes": "string",
  "notes_html": "string",
  "IM": "string",
  "IM2": "string",
  "IM3": "string",
  "government_id": "string",
  "customer_id": "string",
  "file_as": "string",
  "account_name": "string",
  "blackberry_pin": "string",
  "picture": "string",
  "attachments": [
    {
      "name": "string",
      "mime_type": "string",
      "size": 0,
      "id": 0
    }
  ],
  "folder": 0,
  "permissions": {
    "can_edit": true,
    "can_delete": true
  }
}
```

**Contact Object Properties**

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Contact GUID |
| `name` | string | FullName or Group Name |
| `group` | boolean | Whether contact is a group |
| `emails` | object | Email addresses |
| `name_details` | object | Name components |
| `categories` | string | Comma delimited list |
| `web_addresses` | array | Web addresses with type ("business", "home") |
| `company` | string | Company name |
| `title` | string | Job title |
| `office` | string | Office location |
| `department` | string | Department name |
| `assistant` | string | Assistant name |
| `manager` | string | Manager name |
| `addresses` | array | Physical addresses with type ("home", "business", "other") |
| `phone` | object | Phone numbers |
| `birthday` | string | ISO-8601 Date (YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `anniversary` | string | ISO-8601 Date (YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `notes` | string | Plain text notes (appears in NOTES field of vCard) |
| `notes_html` | string | HTML formatted notes (appears in X-ALT-NOTE field of vCard) |
| `picture` | string | Base64 encoded image string |
| `attachments` | array | Contact attachments |
| `folder` | number | Folder ID |
| `permissions` | object | User permissions for this contact |

**Group Contact Response**

When `group` is `true`, the response includes:

```json
{
  "id": "string",
  "name": "string",
  "group": true,
  "sort_column": "string",
  "sort_ascending": true,
  "members": [
    {
      "guid": "string",
      "name": "string",
      "email": "string"
    }
  ]
}
```

> **Note:** `sort_column` and `sort_ascending` are global, not per contact group. Valid sort_column values: "last_name", "name", "email"

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&id={contact_id}

GET /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}?session={session_id}

GET /WorldClientAPI/contacts/{address_book_name}/contact/{contact_id}?session={session_id}
```

---

### All Contacts

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number (defaults to first page) |
| `max_per_page` | number | No | Maximum contacts per page (has default setting) |

**Response - Success**

```json
{
  "contacts": [],
  "permissions": {
    "can_create": true,
    "can_delete": true,
    "can_edit": true,
    "can_read": true,
    "can_write": true
  },
  "sort_column": "string",
  "sort_ascending": true,
  "page": 1,
  "pages": 10
}
```

The `contacts` array contains contact objects like Single Contact minus: `attachments`, `picture`, `permissions`

Plus the following properties:

| Property | Type | Description |
|----------|------|-------------|
| `has_attachments` | boolean | Whether contact has attachments |
| `has_picture` | boolean | Whether contact has a picture |

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&page={page_number}&max_per_page={max_messages_per_page}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&page={page_number}&max_per_page={max_messages_per_page}

GET /WorldClientAPI/contacts/{folder_id}/page/{page_number}?session={session_id}&max_per_page={max_per_page}

GET /WorldClientAPI/contacts/{address_book_name}/page/{page_number}?session={session_id}&max_per_page={max_per_page}
```

---

### Autocomplete Contacts

The differences between an autocomplete response and a regular get contacts response are how the `search` is handled and what is returned. Each email address for a given contact is included as a separate contact item.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `autocomplete` | number | Yes | Set to `1` |
| `search` | string | No | Search term |

**Response - Success**

```json
{
  "contacts": [
    {
      "guid": "string",
      "name": "string",
      "email": "string",
      "group": false,
      "folder": 0
    }
  ],
  "permissions": {
    "can_create": true,
    "can_delete": true,
    "can_edit": true,
    "can_read": true,
    "can_write": true
  },
  "sort_column": "string",
  "sort_ascending": true,
  "page": 1,
  "pages": 10
}
```

| Property | Type | Description |
|----------|------|-------------|
| `guid` | string | Contact GUID |
| `name` | string | Contact name |
| `email` | string | Email address (email, email2, or email3; comma delimited for groups) |
| `group` | boolean | Whether contact is a group |
| `folder` | number | Folder ID where contact comes from |

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&page={page_number}&autocomplete=1

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&page={page_number}&autocomplete=1

GET /WorldClientAPI/contacts/{folder_id}/page/{page_number}?session={session_id}&autocomplete=1

GET /WorldClientAPI/contacts/{address_book_name}/page/{page_number}?session={session_id}&autocomplete=1
```

---

### Common Contacts

An array of contacts that a given user commonly chooses when sending an email. Used by the client to output the top three common contacts that match a search when an auto complete input field is used.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `common` | number | Yes | Set to `1` |

**Response - Success**

```json
{
  "common_contacts": [
    [
      "name",
      "email",
      {
        "date": 0,
        "used": 0,
        "guid": "string"
      }
    ]
  ]
}
```

| Property | Type | Description |
|----------|------|-------------|
| `name` | string | Contact name |
| `email` | string | Contact email |
| `date` | number | UTC time in milliseconds for the last used moment |
| `used` | number | Number of times the contact has been used in auto complete |
| `guid` | string | Contact's GUID |

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&address_book=personal&common=1

GET /WorldClientAPI/contacts/personal/common?session={session_id}
```

---

### Sort Contacts

To change the sort order and sort column, include these parameters with any GET request for multiple contacts.

**sort_column options:**

| Value | Description |
|-------|-------------|
| `name` | Sort by name |
| `last_name` | Sort by last name |
| `email` | Sort by email |
| `company` | Sort by company |
| `notes` | Sort by notes |

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sort_column` | string | Yes | Column to sort by |
| `sort_ascending` | boolean | Yes | Sort direction |
| `page` | number | Yes | Page number |

**Response - Success**

```json
{
  "contacts": [],
  "permissions": {
    "can_edit": true,
    "can_delete": true,
    "can_write": true
  },
  "sort_column": "string",
  "sort_ascending": true,
  "page": 1,
  "pages": 10
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "sort_column"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}

GET /WorldClientAPI/contacts/{folder_id}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}

GET /WorldClientAPI/contacts/{address_book_name}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}
```

---

### Search Contacts

The search value searches on the following properties:

- name
- nick_name
- email, email2, email3
- company
- department
- title
- phone (business, home, home_mobile)
- addresses (home city, home state, business city, business state, other city, other state)

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | string | Yes | Search term |
| `page` | number | Yes | Page number |

**Response - Success**

```json
{
  "contacts": []
}
```

The `contacts` array is filled with contact objects like the All Contacts GET.

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "search"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&page={page_number}&search={string}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&page={page_number}&search={string}

GET /WorldClientAPI/contacts/{folder_id}/page/{page_number}?session={session_id}&search={string}

GET /WorldClientAPI/contacts/{address_book_name}/page/{page_number}?session={session_id}&search={string}
```

---

### Sort Group Members

The group sort settings are per user, not per group.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | No | Contact ID (include to get sorted members back) |
| `group` | number | Yes | Set to `1` |
| `sort_column` | string | No | "last_name", "name", or "email" |
| `sort_ascending` | boolean | No | Sort direction |

> **Note:** Either `sort_column` or `sort_ascending` or both can be sent.

**Response - Success**

```json
{
  "success": true,
  "sort_column": "string",
  "sort_ascending": true,
  "members": [
    {
      "guid": "string",
      "name": "string",
      "email": "string"
    }
  ]
}
```

> **Note:** The `members` array is only included if the optional `id` parameter was provided.

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "sort_column"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&group=1&id={contact_id}&sort_column={string}&sort_ascending={boolean}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&group=1&sort_column={string}&sort_ascending={boolean}

GET /WorldClientAPI/contacts/{folder_id}?session={session_id}&group=1&sort_column={string}&sort_ascending={boolean}

GET /WorldClientAPI/contacts/{address_book_name}?session={session_id}&group=1&sort_column={string}&sort_ascending={boolean}
```

---

### Download Attachment

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID |
| `attachment` | number | Yes | Attachment ID |

> **Note:** `download={attachment_id}` is deprecated.

**Response - Success**

Download begins.

**Response - Failure**

| Status Code | Description |
|-------------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&attachment={attachment_id}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&id={contact_id}&attachment={attachment_id}

GET /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/attachment/{attachment_id}?session={session_id}

GET /WorldClientAPI/contacts/{address_book_name}/contact/{contact_id}/attachment/{attachment_id}?session={session_id}
```

---

### Export Contact vCard

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID |
| `vcard` | number | Yes | Set to `1` |

**Response - Success**

Download begins.

**Response - Failure**

| Status Code | Description |
|-------------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&vcard=1

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&id={contact_id}&vcard=1

GET /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/vcard?session={session_id}

GET /WorldClientAPI/contacts/{address_book_name}/contact/{contact_id}/vcard?session={session_id}
```

---

### Export Contacts as CSV

Allows users to export a CSV file of contacts from a specific folder. The primary export can be imported into Outlook. The `groups` export can only be imported to other MDaemon Webmail clients.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `export` | number | Yes | Set to `1` |
| `groups` | number | No | Set to `1` for MDaemon specific export |

**Response - Success**

Download begins.

**Response - Failure**

| Status Code | Description |
|-------------|-------------|
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&export=1&groups=1

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&export=1

GET /WorldClientAPI/contacts/{folder_id}/export/contacts.csv?session={session_id}&groups=1

GET /WorldClientAPI/contacts/{address_book_name}/export/contacts.csv?session={session_id}
```

---

### Companies

Get a list of companies.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `companies` | number | Yes | Set to `1` |

**Response - Success**

```json
{
  "companies": {
    "CompanyName": {
      "name": "string",
      "has_departments": true
    }
  }
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&companies=1

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&companies=1

GET /WorldClientAPI/contacts/{folder_id}/companies

GET /WorldClientAPI/contacts/{address_book_name}/companies
```

---

### Company Departments

A list of departments given a specific company.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `departments` | number | Yes | Set to `1` |
| `company` | string | Yes | Company name |

**Response - Success**

```json
{
  "departments": {
    "DepartmentName": {
      "name": "string"
    }
  }
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "company"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&departments=1&company={string}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&departments=1&company={string}

GET /WorldClientAPI/contacts/{folder_id}/departments?company={string}

GET /WorldClientAPI/contacts/{address_book_name}/departments?company={string}
```

---

### Department Contacts

A list of contacts given a specific company and department.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `departments` | number | Yes | Set to `1` |
| `contacts` | number | Yes | Set to `1` |
| `department` | string | Yes | Department name |
| `company` | string | Yes | Company name |

**Response - Success**

```json
{
  "departments": [
    {
      "name": "string"
    }
  ]
}
```

> **Note:** Contacts with a company and no department will be listed under 'Uncategorized'.

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "company"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&departments=1&contacts=1&company={string}&department={string}

GET /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&departments=1&contacts=1&company={string}&department={string}

GET /WorldClientAPI/contacts/{folder_id}/departments/contacts?company={string}&department={string}

GET /WorldClientAPI/contacts/{address_book_name}/departments/contacts?company={string}&department={string}
```

---

### Out of Office Status

Information regarding the status of a user's Autoresponder.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `is_autoresponder_enabled` | number | Yes | Set to `1` |
| `contact` | string | Yes | Contact's email address (must be from the same domain as user logged in) |

**Response - Success**

```json
{
  "contact": "string",
  "autoresponder_enabled": true
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "contact"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&is_autoresponder_enabled=1&contact={contact_email_address}

GET /WorldClientAPI/contacts/is_autoresponder_enabled?contact={contact_email_address}
```

---

### Contacts Pictures

Get the contacts' pictures as a base64 encoded binary download.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `pictures` | number | Yes | Set to `1` |
| `contact` | string | No | Contact's email address |

**Response - Success**

```json
{
  "contacts": [
    {
      "id": "string",
      "email": "string",
      "picture": "base64_encoded_string"
    }
  ]
}
```

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Contact GUID |
| `email` | string | Email address of the contact |
| `picture` | string | Base64 encoded JPEG (can be used as `data:image/jpeg;base64` in CSS) |

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "contact"
}
```

or

```json
{
  "error": "InternalError"
}
```

**Example Requests**

```http
GET /WorldClientAPI?session={session_id}&route=contacts&pictures=1&contact={contact_email_address}

GET /WorldClientAPI/contacts/pictures?session={session_id}&contact={contact_email_address}
```

---

## POST Requests

### Upload Attachment

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Conditional | Contact ID (for saved contacts) |
| `new` | number | Conditional | Set to `1` (for unsaved contacts) |
| `attachment` | number | Yes | Set to `1` |

> **Note:** `upload=1` is deprecated. Multiple files are accepted at once.

**Response - Success (Existing Contact)**

```json
[
  {
    "name": "string",
    "mime_type": "string",
    "size": 0,
    "id": 0
  }
]
```

**Response - Success (New/Unsaved Contact)**

```json
[
  {
    "mime_type": "string",
    "name": "string",
    "size": 0,
    "temp": "string"
  }
]
```

> **Note:** When the contact is sent to the server, the attachments will be added to the contact.

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&attachment=1

POST /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&new=1&attachment=1

POST /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/attachment?session={session_id}

POST /WorldClientAPI/contacts/{address_book_name}/new/attachment?session={session_id}
```

---

### Upload Picture

The uploaded picture is not saved to a contact until the `create` or `update` request has been made including the picture property that is returned by the upload request.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Conditional | Contact ID (for saved contacts) |
| `new` | number | Conditional | Set to `1` (for unsaved contacts) |
| `picture` | number | Yes | Set to `1` |

> **Note:** `upload_picture=1` is deprecated. Only one file accepted.

**Response - Success**

```json
{
  "picture": "base64_encoded_string"
}
```

The picture can be used as `data:image/jpeg;base64` in CSS.

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&picture=1

POST /WorldClientAPI?session={session_id}&route=contacts&address_book={address_book_name}&new=1&picture=1

POST /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/picture?session={session_id}

POST /WorldClientAPI/contacts/{address_book_name}/new/picture?session={session_id}
```

---

### Move

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID (comma delimited for multiple contacts) |
| `move` | number | Yes | Destination folder ID |

> **Note:** The folder_id must be used instead of the address_book. You can get the folder_id from the contact JSON.

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "move"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id1,contact_id2,etc.}

POST /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}?session={session_id}

POST /WorldClientAPI/contacts/{folder_id}/contact/ids?session={session_id}&id={contact_id1,contact_id2,etc.}
```

POST data:

```
move={move_to_this_folder_id}
```

---

### Copy

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID (comma delimited for multiple contacts) |
| `copy` | number | Yes | Destination folder ID |

> **Note:** The folder_id must be used instead of the address_book. You can get the folder_id from the contact JSON.

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "InvalidParameter",
  "parameter": "copy"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id1,contact_id2,etc.}

POST /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}?session={session_id}

POST /WorldClientAPI/contacts/{folder_id}/contact/ids?session={session_id}&id={contact_id1,contact_id2,etc.}
```

POST data:

```
copy={copy_to_this_folder_id}
```

---

### Import Contact vCard

Upload a VCF file using `multipart/form-data` with the name of the file input being `Attachment`. For details go to the [uploads](uploads.md) page.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vcard` | number | Yes | Set to `1` |

**Response - Success**

```json
{
  "success": true,
  "contact": {
    "email": "string",
    "name": "string"
  },
  "updated": true,
  "exists": true,
  "added": true
}
```

| Property | Type | Description |
|----------|------|-------------|
| `updated` | boolean | Returned if the UUID matches an existing contact |
| `exists` | boolean | Returned if the address matches an existing contact |
| `added` | boolean | Returned if the contact is added |

**Response - Failure**

```json
{
  "error": "Could not import the vCard"
}
```

or

```json
{
  "error": "No vCard file found"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&vcard=1

POST /WorldClientAPI/contacts/{folder_id}/vcard?session={session_id}
```

---

### Import Contacts from CSV

Upload a CSV file using `multipart/form-data` with the name of the file input being `Attachment`. For details go to the [uploads](uploads.md) page.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `import` | number | Yes | Set to `1` |
| `groups` | number | No | Set to `1` for group import |

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "Error importing contacts"
}
```

**Example Requests**

```http
POST /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&import=1&groups=1

POST /WorldClientAPI/contacts/{folder_id}/import?session={session_id}&groups=1
```

---

## PUT Requests

### Create Contact

If using PUT form data, as opposed to `Request={base64_encoded_json_string}` or straight JSON, object properties are represented with `object_name:object_property=object_property_value`.

**Form Data Example:**

```json
{
  "name_details": {
    "first_name": "Tom"
  }
}
```

is equivalent to:

```html
<input type="text" name="name_details:first_name" value="Tom" />
```

which becomes `name_details:first_name=Tom` in the form data PUT to the server.

**Array Form Data Example:**

```json
{
  "web_addresses": [
    {
      "type": "business",
      "address": "https://mywebsite.com"
    }
  ]
}
```

is equivalent to:

```html
<input type="text" name="web_addresses:0:type" value="business" />
<input type="text" name="web_addresses:0:address" value="https://mywebsite.com" />
```

which becomes `web_addresses:0:type=business&web_addresses:0:address=https://mywebsite.com`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `attachments` | string | No | URI encoded attachments array (returned by upload requests with new=1) |
| `group` | boolean | No | Whether creating a group contact |

If sending `Request={base64_encoded_json_string}` you can send:

```json
{
  "name_details": {
    "first_name": "string",
    "last_name": "string"
  }
}
```

**Response - Success**

```json
{
  "success": true,
  "id": "string"
}
```

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
PUT /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}

PUT /WorldClientAPI/contacts/{folder_id}?session={session_id}
```

---

### Update Contact

When updating a contact, submitting an empty property, such as `"name": ""` will result in the property being empty. However, if you do not submit the property at all, it will remain unchanged on the server.

When updating a group contact, all the group members need to be included. There is no DELETE member or PUT member request.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID |
| `group` | boolean | No | Set to `true` for groups |

**Optional Parameters**

> **Note:** You do not include the attachments array when updating a contact.

If sending `Request={base64_encoded_json_string}` you can send:

```json
{
  "name_details": {
    "first_name": "string",
    "last_name": "string"
  }
}
```

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
PUT /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}

PUT /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}?session={session_id}
```

---

### Update Common Contacts

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `common_contacts` | array | Yes | Array of common contacts |

**Request Body Format:**

```json
{
  "common_contacts": [
    [
      "name",
      "email",
      {
        "date": 0,
        "used": 0,
        "guid": "string"
      }
    ]
  ]
}
```

| Property | Type | Description |
|----------|------|-------------|
| `name` | string | Contact name |
| `email` | string | Contact email |
| `date` | number | UTC time in milliseconds for the last used moment |
| `used` | number | Number of times the contact has been used in auto complete |
| `guid` | string | Contact's GUID |

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
PUT /WorldClientAPI?session={session_id}&route=contacts&address_book=personal&common=1

PUT /WorldClientAPI/contacts/personal/common?session={session_id}
```

---

## DELETE Requests

### Delete Contact

To delete multiple contacts pass the ids as comma separated values.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID (comma delimited for multiple) |

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
DELETE /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id1,contact_id2,etc.}

DELETE /WorldClientAPI/contacts/{folder_id}/contact/{contact_id1,contact_id2}?session={session_id}

DELETE /WorldClientAPI/contacts/{folder_id}/contact/ids?session={session_id}&id={contact_id1,contact_id2,etc.}
```

---

### Delete Attachment

This action is only useful for a saved contact. If there is no contact id, you will receive an error message.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID |
| `attachment` | number | Yes | Attachment ID |

**Response - Success**

```json
{
  "attachments": []
}
```

Returns array of remaining attachments.

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
DELETE /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&attachment={attachment_id}

DELETE /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/attachment/{attachment_id}?session={session_id}
```

---

### Delete Picture

This action is only useful for a saved contact. If there is no contact id, you will receive an error message.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Contact ID |
| `picture` | number | Yes | Set to `1` |

**Response - Success**

```json
{
  "success": true
}
```

**Response - Failure**

```json
{
  "error": "string"
}
```

**Example Requests**

```http
DELETE /WorldClientAPI?session={session_id}&route=contacts&folder={folder_id}&id={contact_id}&picture=1

DELETE /WorldClientAPI/contacts/{folder_id}/contact/{contact_id}/picture?session={session_id}
```

---

## Errors

| Error | Description |
|-------|-------------|
| `InvalidParameter` | The parameter value returned had an invalid value |
| `InadequatePermissions` | The attempted action is not permitted on this contact for this user |
| `InvalidRequest` | There were invalid parameters in the request |
