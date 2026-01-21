# Events

Manage calendar events, recurring events, external calendar links, and published calendars.

---

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Table of Contents

### GET Requests
- [Single Event](#single-event)
  - [Recurrence Details](#recurrence-details)
- [All Events](#all-events)
- [Search Events](#search-events)
- [External Calendar Links](#external-calendar-links)
- [Export CSV File](#export-csv-file)
- [Private Access Link](#private-access-link)
- [Published Calendar](#published-calendar)
- [Published Schedule](#published-schedule)
- [Download Attachment](#download-attachment)

### POST Requests
- [Upload Attachment](#upload-attachment)
- [Move](#move)
- [Copy](#copy)
- [Import Events From ICS or CSV File](#import-events-from-ics-or-csv-file)
- [Publish Calendar](#publish-calendar)
- [Publish Schedule](#publish-schedule)
- [Import From External Calendar Links](#import-from-external-calendar-links)
- [Attach Message Attachments to a New Event](#attach-message-attachments-to-a-new-event)

### PUT Requests
- [Create Event](#create-event)
- [Update Event](#update-event)
- [Update Occurrence](#update-occurrence)
- [Add External Calendar Link](#add-external-calendar-link)
- [Update External Calendar Link](#update-external-calendar-link)
- [Update Published Calendar](#update-published-calendar)
- [Update Published Schedule](#update-published-schedule)

### DELETE Requests
- [Delete Event](#delete-event)
- [Delete Occurrence](#delete-occurrence)
- [Delete External Calendar Link](#delete-external-calendar-link)
- [Delete Private Access Link](#delete-private-access-link)
- [Delete Published Calendar](#delete-published-calendar)
- [Delete Published Schedule](#delete-published-schedule)
- [Delete Attachment](#delete-attachment)

---

## Required URI Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `events` |
| `folder` | number | The folder ID |

---

## GET Requests

### Single Event

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | The event ID |
| `occurrence` | string | No | Start date of the occurrence (ISO-8601 Date YYYY-MM-DD) - only useful for recurring events |

**Success Response**

```json
{
  "id": number,
  "subject": string,
  "location": string,
  "notes": string,
  "notes_html": string,
  "start": string,
  "end": string,
  "private": boolean,
  "priority": number,
  "busy_status": number,
  "all_day": boolean,
  "planner": string,
  "attendees": [
    {
      "email": string,
      "type": number,
      "id": number,
      "sent_invite": boolean,
      "response": number,
      "send_invite": boolean
    }
  ],
  "categories": string,
  "reminder": number,
  "recurrence": {
    // detailed below
  },
  "attachments": [
    {
      "name": string,
      "mime_type": string,
      "size": number,
      "id": number
    }
  ],
  "permissions": {
    "can_write": boolean,
    "can_edit": boolean,
    "can_delete": boolean
  }
}
```

**Response Field Details**

| Field | Type | Description |
|-------|------|-------------|
| `notes` | string | text/plain formatted string appears in DESCRIPTION field of ics file |
| `notes_html` | string | text/HTML formatted string appears in X-ALT-DESC field of ics file |
| `start` | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `priority` | number | 0 = Low, 1 = Normal, 2 = High |
| `busy_status` | number | 0 = Free, 1 = Tentative, 2 = Busy, 3 = Out of Office |
| `attendees.type` | number | 0 = Required, 1 = Optional, 2 = Resource |
| `attendees.sent_invite` | boolean | Note: returned property is "sent", when saving use "send_invite" |
| `attendees.response` | number | 0 = None, 1 = Accepted, 2 = Declined, 3 = Tentative |
| `categories` | string | Comma delimited list |
| `reminder` | number | Remind number of minutes before the event |
| `attachments.size` | number | Size in bytes |

**Failure Response**

```json
{
  "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}
```

or

```http
GET /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}
```

---

### Recurrence Details

```json
{
  "recurrence": {
    "end": string,
    "type": number,
    "sub_type": number,
    "interval": number,
    "instance": number,
    "day_of_week_bit_mask": number,
    "day_of_month": number,
    "month_of_year": number,
    "occurrences": number
  }
}
```

**Recurrence Type Values**

| Value | Description |
|-------|-------------|
| 0 | Not recurring |
| 1 | Recurs daily |
| 2 | Recurs weekly |
| 3 | Recurs monthly |
| 4 | Recurs yearly |

**Sub-Type Values by Recurrence Type**

**Daily:**
| Value | Description |
|-------|-------------|
| 0 | Recur every `interval` day(s) |
| 1 | Recur every workday |

**Weekly:**
| Value | Description |
|-------|-------------|
| 1 | Recur every `interval` week(s) on `day_of_week_bit_mask` |

**Day of Week Bit Mask:**
| Value | Day |
|-------|-----|
| 1 | Sunday |
| 2 | Monday |
| 4 | Tuesday |
| 8 | Wednesday |
| 16 | Thursday |
| 32 | Friday |
| 64 | Saturday |

**Monthly:**
| Value | Description |
|-------|-------------|
| 2 | Recur every `day_of_month` day every `interval` month(s) |
| 3 | Recur every `instance` (first, second, third, fourth, or last) `day_of_week_bit_mask` every `interval` month(s) |

**Yearly:**
| Value | Description |
|-------|-------------|
| 2 | `day_of_month` `month_of_year` |
| 3 | Every `instance` `day_of_week_bit_mask` `month_of_year` |

**Other Recurrence Fields**

| Field | Description |
|-------|-------------|
| `occurrences` | Number of occurrences for this event |
| `end` | End the occurrences by this date (ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm) |

---

### All Events

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | No | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm (default: end of current day) |

**Success Response**

```json
{
  "events": [
    {
      // Event objects like above minus: recurrence, attachments, attendees
      // Plus the following properties:
      "has_attachments": boolean,
      "recurring": boolean,
      "has_attendees": boolean
    }
  ]
}
```

> **Note:** Recurring events will have the same `id` but different `start` and `end` date properties.

**Failure Response**

```json
{
  "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&start={start_date}&end={end_date}
```

or

```http
GET /WorldClientAPI/events/{folder_id}?session={session_id}&start={start_date}&end={end_date}
```

---

### Search Events

The search value searches on the following properties:
- subject
- notes
- location
- categories

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `search` | string | Yes | Search query |

**Success Response**

```json
{
  "events": [
    {
      // Event objects like above minus: recurrence, attachments, attendees
      // Plus the following properties:
      "has_attachments": boolean,
      "recurring": boolean,
      "has_attendees": boolean
    }
  ]
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
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&start={start_date}&end={end_date}&search={search_query}
```

or

```http
GET /WorldClientAPI/events/{folder_id}?session={session_id}&start={start_date}&end={end_date}&search={search_query}
```

---

### External Calendar Links

Get the array of external calendar links attached to a given calendar.

**Success Response**

```json
{
  "externalCalendars": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ],
  "success": true,
  "permissions": {
    "can_read": true,
    "can_write": boolean,
    "can_delete": boolean
  }
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
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&external=1
```

or

```http
GET /WorldClientAPI/events/{folder_id}/external?session={session_id}
```

---

### Export CSV File

Download a copy of a given calendar in `csv` format.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `export` | number | Yes | Must be `1` |
| `start` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |

**Success Response**

Download begins.

**Failure Response**

- `401 Unauthorized`
- `404 Not Found`
- `500 Internal Server Error`

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&export=1&start={start_date}&end={end_date}
```

or

```http
GET /WorldClientAPI/events/{folder_id}/export/calendar.csv?session={session_id}&start={start_date}&end={end_date}
```

---

### Private Access Link

Get a private access `ics` link for a given calendar. You can also reset the link if you want a new token.

**Requirements:**
- `EnablePublicCalendars` must be set to Yes in the Domains.ini
- Shared folders must be Enabled in MDaemon
- `CanPublishCalendars` must be set to Yes for the User

To change the link used to reach the calendar, send `reset=1` in the URL.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ics` | number | Yes | Must be `1` |
| `reset` | number | No | Set to `1` to reset the link |

**Success Response**

```json
{
  "url": "https://example.com/calendar.ics?token=asoi9091jf0asj"
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
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&ics=1
```

or

```http
GET /WorldClientAPI/events/{folder_id}/ics?session={session_id}
```

or with reset:

```http
GET /WorldClientAPI/events/{folder_id}/ics?session={session_id}&reset=1
```

---

### Published Calendar

Get the details for a published calendar. If the calendar is not published, the `url` will be an empty string.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `publish` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "url": "https://example.com/WorldClient.dll?View=PublicCalendar&CalToken=asoi9091jf0asj",
  "free_busy_url": "https://example.com/WorldClient.dll?View=PublicCalendar&CalToken=abcdefghijkl&freebusy=1",
  "display_name": string,
  "default_view": string,
  "password_required": boolean
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
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&publish=1
```

or

```http
GET /WorldClientAPI/events/{folder_id}/publish?session={session_id}
```

---

### Published Schedule

Get the details for a published schedule.

**Constraints:**
- Meeting length options: 10, 15, 20, 30, or 60
- Max visible schedule options: between 1 and 8 weeks
- Display Name length: 36 characters or less

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `schedule` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "url": "https://example.com/WorldClient.dll?View=Schedule&user={users_email}&id={base64encodedID}",
  "display_name": string,
  "meeting_length": number,
  "max_visible_schedule": number,
  "reminder": number,
  "automatically_accept_meetings": boolean,
  "enabled": boolean,
  "available_days": {
    "su": null,
    "mo": [
      {
        "start": "08:00",
        "end": "11:40"
      },
      {
        "start": "13:00",
        "end": "17:00"
      }
    ],
    "tu": null,
    "we": null,
    "th": null,
    "fr": null,
    "sa": null
  }
}
```

> **Note:** Each day value is either `null` or an array of start/end schedule objects.

**Failure Response**

```json
{
  "error": string
}
```

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&schedule=1
```

or

```http
GET /WorldClientAPI/events/{folder_id}/schedule?session={session_id}
```

---

### Download Attachment

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | The event ID |
| `attachment` | number | Yes | The attachment ID |
| `download` | number | No | Deprecated - use `attachment` instead |

**Success Response**

Download begins.

**Failure Response**

- `401 Unauthorized`
- `404 Not Found`
- `500 Internal Server Error`

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}&attachment={attachment_id}
```

or

```http
GET /WorldClientAPI/events/{folder_id}/event/{event_id}/attachment/{attachment_id}?session={session_id}
```

---

## POST Requests

### Upload Attachment

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Conditional | Event ID - for saved events |
| `new` | number | Conditional | Set to `1` for unsaved events |
| `attachment` | number | Yes | Must be `1` |
| `upload` | number | No | Deprecated - use `attachment` instead |

**Success Response**

```json
{
  "attachments": [
    {
      "name": string,
      "size": number,
      "mime_type": string,
      "temp": string
    }
  ]
}
```

| Field | Description |
|-------|-------------|
| `temp` | Temporary file name on the server |

**Failure Response**

```json
{
  "error": string
}
```

**Example Request (Known Event)**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}&attachment=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/event/{event_id}/attachment?session={session_id}
```

**Example Request (New Event)**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&new=1&attachment=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/new/attachment?session={session_id}
```

---

### Move

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID(s) - comma-separated for multiple |
| `move` | number | Yes | Target folder ID |

**Success Response**

```json
{
  "success": true
}
```

**Failure Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "move"
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id1,event_id2,etc.}
```

or

```http
POST /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}
```

or

```http
POST /WorldClientAPI/events/{folder_id}/event/ids?session={session_id}&id={event_id1,event_id2,etc.}
```

**POST Data:**

```
move={move_to_this_folder_id}
```

---

### Copy

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID(s) - comma-separated for multiple |
| `copy` | number | Yes | Target folder ID |

**Success Response**

```json
{
  "success": true
}
```

**Failure Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "copy"
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id1,event_id2,etc.}
```

or

```http
POST /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}
```

or

```http
POST /WorldClientAPI/events/{folder_id}/event/ids?session={session_id}&id={event_id1,event_id2,etc.}
```

**POST Data:**

```
copy={copy_to_this_folder_id}
```

---

### Import Events From ICS or CSV File

Upload a `csv` or `ics` file using `multipart/form-data` with the name of the file input being `Attachment`. For details go to the [Uploads](uploads.md) page.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `import` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "success": true
}
```

**Failure Response**

```json
{
  "error": "Error importing calendar"
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&import=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/import?session={session_id}
```

---

### Publish Calendar

Get a link to a publicly accessible calendar. Includes an option to require a password, and an option to name the calendar.

**Requirements:**
- `EnablePublicCalendars` must be set to Yes in the Domains.ini
- Shared folders must be Enabled in MDaemon
- `CanPublishCalendars` must be set to Yes for the User

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `publish` | number | Yes | Must be `1` |
| `name` | string | No | Calendar name |
| `default_view` | string | No | `month`, `agendaWeek`, `agendaDay`, or `listMonth` |
| `password` | string | No | Password to protect the calendar |
| `free_busy` | number | No | Set to `1` to get free/busy URL |

**Success Response**

```json
{
  "success": true,
  "url": "https://example.com/WorldClient.dll?View=PublicCalendar&CalToken=asoi9091jf0asj",
  "free_busy_url": "https://example.com/WorldClient.dll?View=PublicCalendar&CalToken=abcdefghi128&freebusy=1"
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
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&publish=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/publish?session={session_id}
```

**POST Data:**

```
default_view={view}
name={name}
password={password}
free_busy=1
```

---

### Publish Schedule

Get a link to a publicly accessible scheduling link for your account on the given calendar.

**Requirements:**
- `EnablePublicCalendars` must be set to Yes in the Domains.ini
- Shared folders must be Enabled in MDaemon
- `CanPublishSchedules` must be set to Yes for the User

**Constraints:**
- Meeting length options: 10, 15, 20, 30, or 60
- Max visible schedule options: between 1 and 8 weeks
- Display Name length: 36 characters or less
- For no `reminder` send any negative number

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `schedule` | number | Yes | Must be `1` |
| `display_name` | string | No | Display name (max 36 characters) |
| `meeting_length` | number | No | 10, 15, 20, 30, or 60 |
| `max_visible_schedule` | number | No | 1-8 weeks |
| `reminder` | number | No | Minutes before event (negative for no reminder) |
| `automatically_accept_meetings` | boolean | No | Auto-accept meetings |
| `enabled` | boolean | No | Enable/disable schedule |
| `available_days` | object | No | Availability per day |

**Success Response**

The response includes the validated data:

```json
{
  "success": true,
  "url": "https://example.com/WorldClient.dll?View=Schedule&user={users_email}&id={base64encodedID}",
  "display_name": string,
  "meeting_length": number,
  "max_visible_schedule": number,
  "reminder": number,
  "automatically_accept_meetings": boolean,
  "enabled": boolean,
  "available_days": {
    "su": null,
    "mo": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "tu": null,
    "we": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "th": null,
    "fr": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "sa": null
  }
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
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&schedule=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/schedule?session={session_id}
```

**POST Data:**

```
display_name={name}
meeting_length={length}
max_visible_schedule={weeks}
reminder={minutes}
automatically_accept_meetings={true|false}
enabled={true|false}
available_days={object}
```

---

### Import From External Calendar Links

Imports/Reimports the `ics` files to the given calendar from all external calendar links. Returns an array of `errors` that has any links which failed to import. Failed imports could have expired links, or errors in the ics file.

**Success Response**

```json
{
  "externalCalendars": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ],
  "permissions": {
    "can_read": true,
    "can_write": boolean,
    "can_remove": boolean
  }
}
```

If link errors exist:

```json
{
  "externalCalendars": [...],
  "permissions": {...},
  "error": "Some external calendars failed to update.",
  "errors": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ]
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
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&external=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/external?session={session_id}
```

---

### Attach Message Attachments to a New Event

Adds the attachments from a message to a new event. When the event is saved, include the attachments list, and they will be included in the new event.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `attach_from_message` | number | Yes | Must be `1` |
| `mail_folder_id` | number | Yes | The mail folder ID |
| `message_id` | number | Yes | The message ID |

**Success Response**

```json
[
  {
    "name": string,
    "size": number,
    "mime_type": string,
    "temp": string
  }
]
```

| Field | Description |
|-------|-------------|
| `temp` | Temporary file name on the server |

**Failure Response**

```json
{
  "error": "Internal Server Error"
}
```

or

```json
{
  "error": "MissingParameter",
  "parameter": "mail_folder_id"
}
```

or

```json
{
  "error": "InadequatePermissions"
}
```

**Example Request**

```http
POST /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&new=1&attachment=1
```

or

```http
POST /WorldClientAPI/events/{folder_id}/new/attachment?session={session_id}
```

**POST Data:**

```
attach_from_message=1
mail_folder_id={mail_folder_id}
message_id={message_id}
```

---

## PUT Requests

### Create Event

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `subject` | string | No | Event subject |
| `location` | string | No | Event location |
| `start` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `notes` | string | No | Event notes |
| `priority` | number | No | 0 = Low, 1 = Normal, 2 = High |
| `busy_status` | number | No | 0 = Free, 1 = Tentative, 2 = Busy, 3 = Out of Office |
| `private` | boolean | No | Mark as private |
| `all_day` | boolean | No | All day event |
| `categories` | string | No | Comma delimited list |
| `reminder` | number | No | Minutes before the event |
| `attachments` | string | No | URI encoded JSON attachments array (returned by upload requests with new=1) |

**Attendee Parameters (URL-encoded form):**

```
attendees:0:email={email}
attendees:0:type={type}
attendees:0:response={response}
attendees:0:send_invite={true|false}
attendees:1:email={email}
...
```

**Recurrence Parameters (URL-encoded form):**

```
recurrence:end={date}
recurrence:type={type}
recurrence:sub_type={sub_type}
recurrence:interval={interval}
recurrence:instance={instance}
recurrence:day_of_week_bit_mask={mask}
recurrence:day_of_month={day}
recurrence:month_of_year={month}
recurrence:occurrences={count}
```

**Base64 JSON Format:**

If sending a `Request={base_64_encoded_json_string}`, you can send:

```json
{
  "recurrence": {
    "type": number,
    "end": string
  },
  "attendees": [
    {
      "email": string,
      "type": number,
      "send_invite": boolean
    }
  ]
}
```

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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}
```

or

```http
PUT /WorldClientAPI/events/{folder_id}?session={session_id}
```

---

### Update Event

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID |
| `subject` | string | Yes | Event subject |
| `start` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| `end` | string | Yes | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |

**Optional Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `location` | string | Event location |
| `notes` | string | Event notes |
| `priority` | number | 0 = Low, 1 = Normal, 2 = High |
| `busy_status` | number | 0 = Free, 1 = Tentative, 2 = Busy, 3 = Out of Office |
| `private` | boolean | Mark as private |
| `all_day` | boolean | All day event |
| `categories` | string | Comma delimited list |
| `reminder` | number | Minutes before the event |

**Attendee Parameters (URL-encoded form):**

```
attendees:0:email={email}
attendees:0:type={type}
attendees:0:response={response}
attendees:0:send_invite={true|false}
...
```

**Recurrence Parameters (URL-encoded form):**

```
recurrence:end={date}
recurrence:type={type}
recurrence:sub_type={sub_type}
recurrence:interval={interval}
recurrence:instance={instance}
recurrence:day_of_week_bit_mask={mask}
recurrence:day_of_month={day}
recurrence:month_of_year={month}
recurrence:occurrences={count}
```

> **Note:** You do not include the attachments array when updating an event.

**Base64 JSON Format:**

If sending a `Request={base_64_encoded_json_string}`, you can send:

```json
{
  "recurrence": {
    "start": string,
    "end": string
  },
  "attendees": [
    {
      "email": string,
      "type": number,
      "send_invite": boolean
    }
  ]
}
```

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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}
```

---

### Update Occurrence

Individual occurrences of recurring events can be changed. However, only a limited set of properties can be changed.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID |
| `occurrence` | string | Yes | Start date of the occurrence being edited (ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `start` | string | Yes | New start date (ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `end` | string | Yes | New end date (ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm) |

**Optional Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `subject` | string | Event subject |
| `location` | string | Event location |
| `notes` | string | Event notes |
| `busy_status` | number | 0 = Free, 1 = Tentative, 2 = Busy, 3 = Out of Office |
| `categories` | string | Comma delimited list |
| `reminder` | string | Number of minutes before the event |

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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}&occurrence={date_of_occurrence}&start={new_start_date}&end={new_end_date}
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}&occurrence={date_of_occurrence}&start={new_start_date}&end={new_end_date}
```

---

### Add External Calendar Link

Adds a single external calendar link to a given calendar.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `uri` | string | Yes | External calendar link URL |

**Success Response**

```json
{
  "success": true,
  "externalCalendars": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ],
  "permissions": {
    "can_read": true,
    "can_write": boolean,
    "can_delete": boolean
  }
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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&external=1
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/external?session={session_id}
```

**PUT Data:**

```
uri={external_calendar_link}
```

---

### Update External Calendar Link

Edits a single external calendar link by the index in the array of external calendar links.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `uri` | string | Yes | External calendar link URL |
| `index` | number | Yes | Index of external calendar link to update |

**Success Response**

```json
{
  "success": true,
  "externalCalendars": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ],
  "permissions": {
    "can_read": true,
    "can_write": boolean,
    "can_delete": boolean
  }
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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&external=1&index={index_of_edited_link}
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/external/{index_of_edited_link}?session={session_id}
```

**PUT Data:**

```
uri={external_calendar_link}
```

---

### Update Published Calendar

If you wish to add a name or password to an already published calendar you will need to do it this way.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `publish` | number | Yes | Must be `1` |
| `password` | string | No | Pass empty to clear the password, do not pass to avoid changing it |
| `name` | string | No | Pass empty to clear the name, do not pass to avoid changing it |
| `default_view` | string | No | `month`, `agendaWeek`, `agendaDay`, or `listMonth` |
| `free_busy` | number | No | Set to `1` to get free/busy URL in response |

**Success Response**

```json
{
  "success": true,
  "free_busy_url": string
}
```

> **Note:** `free_busy_url` is only included if `free_busy=1` is included in the PUT request.

**Failure Response**

```json
{
  "error": "Password invalid"
}
```

**Example Request**

```http
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&publish=1
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/publish?session={session_id}
```

**PUT Data:**

```
password={password}
name={name}
```

---

### Update Published Schedule

Make changes to an existing publicly accessible schedule for your account on the given calendar.

**Requirements:**
- `EnablePublicCalendars` must be set to Yes in the Domains.ini
- Shared folders must be Enabled in MDaemon
- `CanPublishSchedules` must be set to Yes for the User

**Constraints:**
- Meeting length options: 10, 15, 20, 30, or 60
- Max visible schedule options: between 1 and 8 weeks
- Display Name length: 36 characters or less
- For no `reminder` send any negative number

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `schedule` | number | Yes | Must be `1` |
| `display_name` | string | No | Display name (max 36 characters) |
| `meeting_length` | number | No | 10, 15, 20, 30, or 60 |
| `max_visible_schedule` | number | No | 1-8 weeks |
| `reminder` | number | No | Minutes before event (negative for no reminder) |
| `automatically_accept_meetings` | boolean | No | Auto-accept meetings |
| `enabled` | boolean | No | Enable/disable schedule |
| `available_days` | object | No | Availability per day |

**Success Response**

The response includes the validated data:

```json
{
  "success": true,
  "url": "https://example.com/WorldClient.dll?View=Schedule&user={users_email}&id={base64encodedID}",
  "display_name": string,
  "meeting_length": number,
  "max_visible_schedule": number,
  "reminder": number,
  "automatically_accept_meetings": boolean,
  "enabled": boolean,
  "available_days": {
    "su": null,
    "mo": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "tu": null,
    "we": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "th": null,
    "fr": [
      {
        "start": "08:00",
        "end": "12:00"
      },
      {
        "start": "14:00",
        "end": "18:00"
      }
    ],
    "sa": null
  }
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
PUT /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&schedule=1
```

or

```http
PUT /WorldClientAPI/events/{folder_id}/schedule?session={session_id}
```

**PUT Data:**

```
display_name={name}
meeting_length={length}
max_visible_schedule={weeks}
reminder={minutes}
automatically_accept_meetings={true|false}
enabled={true|false}
available_days={object}
```

---

## DELETE Requests

### Delete Event

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID(s) - comma delimited for multiple events |

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
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id1,event_id2,etc.}
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/event/ids?session={session_id}&id={event_id1,event_id2,etc.}
```

---

### Delete Occurrence

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID (if multiple IDs sent, only the first is read) |
| `occurrence` | string | Yes | ISO-8601 Date YYYY-MM-DD |

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
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}&occurrence={occurrence}
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/event/{event_id}?session={session_id}&occurrence={occurrence}
```

---

### Delete External Calendar Link

Deletes a single external calendar link from a given calendar.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `index` | number | Yes | Index of external calendar link to delete |

**Success Response**

```json
{
  "success": true,
  "externalCalendars": [
    "https://example.com/calendar/12879abakdsf81919asdakfjsdk"
  ],
  "permissions": {
    "can_read": true,
    "can_write": boolean,
    "can_delete": boolean
  }
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
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&external=1&index={index_of_external_calendar_link}
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/external/{index_of_external_calendar_link}?session={session_id}
```

---

### Delete Private Access Link

Delete the private access `ics` link for a given calendar.

**Requirements:**
- `EnablePublicCalendars` must be set to Yes in the Domains.ini
- Shared folders must be Enabled in MDaemon
- `CanPublishCalendars` must be set to Yes for the User

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ics` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "url": "https://example.com/calendar.ics?token=asoi9091jf0asj"
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
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&ics=1
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/ics?session={session_id}
```

---

### Delete Published Calendar

Unpublish a calendar from a given calendar.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `publish` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "success": true
}
```

**Failure Response**

```json
{
  "error": "No token associated with this Calendar"
}
```

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&publish=1
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/publish?session={session_id}
```

---

### Delete Published Schedule

Unpublish a schedule from a given calendar.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `schedule` | number | Yes | Must be `1` |

**Success Response**

```json
{
  "success": true
}
```

**Failure Response**

```json
{
  "error": "No token associated with this Calendar"
}
```

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&schedule=1
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/schedule?session={session_id}
```

---

### Delete Attachment

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Yes | Event ID |
| `attachment` | number | Yes | Attachment ID |

**Success Response**

```json
{
  "attachments": []
}
```

> **Note:** Returns array of remaining attachments.

**Failure Response**

```json
{
  "error": string
}
```

**Example Request**

```http
DELETE /WorldClientAPI?session={session_id}&route=events&folder={folder_id}&id={event_id}&attachment={attachment_id}
```

or

```http
DELETE /WorldClientAPI/events/{folder_id}/event/{event_id}/attachment/{attachment_id}?session={session_id}
```

---

## Errors

| Error | Description |
|-------|-------------|
| `InvalidParameter` | The parameter value returned had an invalid value |
| `InadequatePermissions` | The attempted action is not permitted on this event for this user |
| `InvalidRequest` | There were invalid parameters in the request |
