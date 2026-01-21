# Free/Busy

> Free/busy information and meeting scheduling for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [GET Requests](#get-requests)
  - [Attendee Free/Busy Schedule](#attendee-freebusy-schedule)
  - [Auto Pick Meeting Start Time](#auto-pick-meeting-start-time)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `freebusy` |

---

## GET Requests

### Attendee Free/Busy Schedule

Get an array of attendees with their event/meeting schedule.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `attendees` | string | Yes | Comma-delimited string of attendee email addresses |
| `start` | string | Yes | Start date (ISO-8601: YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `end` | string | Yes | End date (ISO-8601: YYYY-MM-DDTHH:mm:ss+/-HH:mm) |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=freebusy&attendees={attendees}&start={start}&end={end}
```

```http
GET /WorldClientAPI/freebusy?session={session_id}&attendees={attendees}&start={start}&end={end}
```

**Success Response**

```json
{
  "attendees": [
    {
      "email": string,
      "available": boolean,
      "schedule": [
        {
          "status": number,
          "start": string,
          "end": string
        }
      ]
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `attendees` | array | Array of attendee schedule objects |
| `attendees[].email` | string | Attendee email address |
| `attendees[].available` | boolean | Whether free/busy info is available for this user |
| `attendees[].schedule` | array | Array of time blocks |
| `attendees[].schedule[].status` | number | Status code (see below) |
| `attendees[].schedule[].start` | string | Block start (ISO-8601) |
| `attendees[].schedule[].end` | string | Block end (ISO-8601) |

**Status Codes**

| Value | Status |
|-------|--------|
| 0 | Free |
| 1 | Tentative |
| 2 | Busy |
| 3 | Out of Office |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "attendees | start | end"
}
```

---

### Auto Pick Meeting Start Time

Get the previous or next start time when all attendees will be available for the requested duration.

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `autopickstart` | number | Yes | Must be `1` |
| `attendees` | string | Yes | Comma-delimited string of attendee email addresses |
| `duration` | number | Yes | Duration of the event in minutes |
| `start` | string | Yes | Starting point for search (ISO-8601: YYYY-MM-DDTHH:mm:ss+/-HH:mm) |
| `forward` | boolean | No | Search forward in time (default: backward) |

**Example Request**

```http
GET /WorldClientAPI?session={session_id}&route=freebusy&autopickstart=1&duration={duration}&attendees={attendees}&start={start}
```

```http
GET /WorldClientAPI/freebusy/autopickstart/{duration}?session={session_id}&attendees={attendees}&start={start}
```

**Success Response**

```json
{
  "autopickstart": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `autopickstart` | string | Suggested meeting start time (ISO-8601) |

**Error Response**

```json
{
  "error": "InvalidParameter",
  "parameter": "attendees | start | duration"
}
```
