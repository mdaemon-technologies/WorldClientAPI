[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

# Tasks

## Requests

### GET
- [Single Task](#single-task)
  - [Recurrence Details](#recurrence-details)
- [All Tasks](#all-tasks)
- [Sort Tasks](#sort-tasks)
- [Search Tasks](#search-tasks)
- [Download Attachment](#download-attachment)

### POST
- [Upload Attachment](#upload-attachment)
- [Move](#move)
- [Copy](#copy)
- [Attach Message Attachments to a New Task](#attach-message-attachments-to-a-new-task)

### PUT
- [Create Task](#create-task)
- [Update Task](#update-task)
  - [Remove Recurrence](#remove-recurrence)
- [Complete Task](#complete-task)

### DELETE
- [Delete Task](#delete-task)
- [Delete Occurrence](#delete-occurrence)
- [Delete Attachment](#delete-attachment)

---

## Required URI Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| route | string | `tasks` |
| folder | number | folder_id |

---

## GET

### Single Task

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |

**Responses**

*success*

```json
{
    "id": number,
    "subject": string,
    "notes": string,
    "notes_html": string,
    "overdue": boolean,
    "completed": boolean,
    "completed_date": string,
    "percent_complete": number,
    "priority": number,
    "status": number,
    "private": boolean,
    "due": string,
    "start": string,
    "categories": string,
    "reminder": string,
    "estimated_hours": number,
    "actual_hours": number,
    "mileage": string,
    "billing": string,
    "companies": string,
    "recurrence": {
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
        "can_edit": boolean,
        "can_delete": boolean
    }
}
```

| Field | Type | Description |
|-------|------|-------------|
| id | number | Task identifier |
| subject | string | Task subject |
| notes | string | text/plain formatted string appears in DESCRIPTION field of ics file |
| notes_html | string | text/HTML formatted string appears in X-ALT-DESC field of ics file |
| overdue | boolean | Whether task is overdue |
| completed | boolean | Whether task is completed |
| completed_date | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| percent_complete | number | 0 to 100 |
| priority | number | 0 = Low, 1 = Normal, 2 = High |
| status | number | 0 = Not Started, 1 = In Progress, 2 = Completed, 3 = Waiting on someone else, 4 = Deferred |
| private | boolean | Whether task is private |
| due | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| categories | string | comma delimited list |
| reminder | string | ISO-8601 Date YYYY-MM-DDTHH:mm+/-HH:mm |
| estimated_hours | number | Estimated hours for task |
| actual_hours | number | Actual hours spent |
| mileage | string | Mileage information |
| billing | string | Billing information |
| companies | string | Associated companies |
| recurrence | object | Returned when the task is recurring (detailed below) |
| attachments | array | Array of attachment objects |
| permissions | object | User permissions for this task |

*failure*

```json
{
    "error": string
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}
```

or

```
GET /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

---

### Recurrence Details

```json
"recurrence": {
    "start": string,
    "end": string,
    "type": number,
    "sub_type": number,
    "interval": number,
    "instance": number,
    "day_of_week_bit_mask": number,
    "day_of_month": number,
    "month_of_year": number,
    "occurrences": number,
    "sliding": boolean,
    "last_occurrence": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| end | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| type | number | Recurrence type |
| sub_type | number | Recurrence sub-type |
| interval | number | Recurrence interval |
| instance | number | Instance number |
| day_of_week_bit_mask | number | Bitmask for days of week |
| day_of_month | number | Day of month |
| month_of_year | number | Month of year |
| occurrences | number | Number of occurrences |
| sliding | boolean | Whether recurrence slides |
| last_occurrence | boolean | True if the task is the last occurrence of a series |

#### Type Values

| Value | Description |
|-------|-------------|
| 0 | Not recurring |
| 1 | Recurs daily |
| 2 | Recurs weekly |
| 3 | Recurs monthly |
| 4 | Recurs yearly |

#### Sub-Type Values

**Daily**

| Value | Description |
|-------|-------------|
| 0 | Recur every `interval` day(s). If `sliding` is true, regenerate new task `interval` day(s) after each task is completed |
| 1 | Recur every workday |

**Weekly**

| Value | Description |
|-------|-------------|
| 1 | Recur every `interval` week(s) |
| 0 | (`sliding` must be true) Regenerate new task `interval` week(s) after each task is completed |

**day_of_week_bit_mask** (send 3 for Sunday and Monday, 5 for Sunday and Tuesday, etc.)

| Value | Day |
|-------|-----|
| 1 | Sunday |
| 2 | Monday |
| 4 | Tuesday |
| 8 | Wednesday |
| 16 | Thursday |
| 32 | Friday |
| 64 | Saturday |

**Monthly**

| Value | Description |
|-------|-------------|
| 2 | Recur every `day_of_month` day every `interval` month(s). If `sliding` is true, regenerate new task `interval` month(s) after each task is completed |
| 3 | Recur every `instance` (first, second, third, fourth, or last) `day_of_week_bit_mask` every `interval` month(s) |

**Yearly**

| Value | Description |
|-------|-------------|
| 2 | `day_of_month` `month_of_year`. If `sliding` is true, regenerate new task `interval` year(s) after each task is completed |
| 3 | Every `instance` `day_of_week_bit_mask` `month_of_year` |

#### Additional Notes

- **occurrences**: Number of occurrences for this task
- **end**: End the occurrences by this date. For no end date, send the year `4500` like so `4500-01-01T00:00:00+05:00`
- **last_occurrence**: True if the task is the last occurrence of a series

---

### All Tasks

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| page | number | Page number |
| max_per_page | number | Optional - uses default setting if not provided |

**Responses**

*success*

```json
{
    "tasks": [],
    "permissions": {
        "can_edit": boolean,
        "can_delete": boolean,
        "can_write": boolean
    },
    "sort_column": string,
    "sort_ascending": boolean,
    "page": number,
    "pages": number
}
```

| Field | Type | Description |
|-------|------|-------------|
| tasks | array | Array of task objects |
| permissions | object | User permissions for this folder |
| sort_column | string | Current sort column |
| sort_ascending | boolean | Sort direction |
| page | number | Current page |
| pages | number | Total pages |

`tasks` is filled with task objects like above minus the following properties:

- notes, recurrence, attachments, reminder, estimated_hours, actual_hours, mileage, billing, companies, private

plus the following properties:

| Field | Type | Description |
|-------|------|-------------|
| has_attachments | boolean | Whether task has attachments |
| recurring | boolean | Whether task is recurring |

*failure*

```json
{
    "error": string
}
```

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&page={page_number}
```

or

```
GET /WorldClientAPI/tasks/{folder_id}/page/{page_number}?session={session_id}
```

---

### Sort Tasks

To change the sort order and sort column, include these parameters with any GET request for multiple tasks.

**sort_column options:**

`"complete"`, `"start_date"`, `"due_date"`, `"status"`, `"subject"`, `"categories"`, `"priority"`

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
    "tasks": []
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
GET /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&page={page_number}&sort_column={sort_column}&sort_ascending={boolean}
```

or

```
GET /WorldClientAPI/tasks/{folder_id}/page/{page_number}?session={session_id}&sort_column={sort_column}&sort_ascending={boolean}
```

---

### Search Tasks

The search value searches on the following properties:

- subject
- notes
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
    "tasks": []
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
GET /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&page={page_number}&search={string}
```

or

```
GET /WorldClientAPI/tasks/{folder_id}/page/{page_number}?session={session_id}&search={string}
```

---

### Download Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |
| attachment | number | attachment_id |

or

| Parameter | Type | Description |
|-----------|------|-------------|
| download | number | attachment_id (deprecated) |

**Responses**

*success*

Download begins

*failure*

- `401 Unauthorized`
- `404 Not Found`
- `500 Internal Server Error`

**Example Request**

```
GET /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}&attachment={attachment_id}
```

or

```
GET /WorldClientAPI/tasks/{folder_id}/task/{task_id}/attachment/{attachment_id}?session={session_id}
```

---

## POST

### Upload Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id (for saved tasks) |
| new | number | 1 (for unsaved tasks) |
| attachment | number | 1 |

or

| Parameter | Type | Description |
|-----------|------|-------------|
| upload | number | 1 (deprecated) |

**Responses**

*success*

```json
{
    "attachments": []
}
```

*failure*

```json
{
    "error": string
}
```

**Example Request**

Known Task:

```
POST /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}&attachment=1
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/task/{task_id}/attachment?session={session_id}
```

New Task:

```
POST /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&new=1&attachment=1
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/new/attachment?session={session_id}
```

---

### Move

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |
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
POST /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id1,task_id2,etc}
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/task/ids?session={session_id}&id={task_id1,task_id2,etc.}
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
| id | number | task_id |
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
POST /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id1,task_id2,etc.}
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/task/ids?session={session_id}&id={task_id1,task_id2,etc.}
```

POST data:

```
copy={copy_to_this_folder_id}
```

---

### Attach Message Attachments to a New Task

Adds the attachments from a message to a new task. When the task is saved, include the attachments list, and they will be included in the new task.

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
        "temp": string
    }
]
```

| Field | Type | Description |
|-------|------|-------------|
| name | string | Attachment name |
| size | number | Attachment size in bytes |
| mime_type | string | MIME type |
| temp | string | Temporary file name on the server |

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

New Task:

```
POST /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&new=1&attachment=1
```

or

```
POST /WorldClientAPI/tasks/{folder_id}/new/attachment?session={session_id}
```

POST data:

```
attach_from_message=1
mail_folder_id={number}
message_id={number}
```

---

## PUT

### Create Task

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| subject | string | Task subject |
| notes | string | Task notes |
| completed | boolean | Completion status |
| completed_date | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| percent_complete | number | Completion percentage (0-100) |
| priority | number | Priority level |
| status | number | Task status |
| private | boolean | Private flag |
| estimated_hours | number | Estimated hours |
| actual_hours | number | Actual hours |
| mileage | string | Mileage information |
| billing | string | Billing information |
| companies | string | Associated companies |
| due | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| categories | string | Comma delimited list |
| reminder | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:end | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:type | number | Recurrence type |
| recurrence:sub_type | number | Recurrence sub-type |
| recurrence:interval | number | Recurrence interval |
| recurrence:instance | number | Instance number |
| recurrence:day_of_week_bit_mask | number | Day of week bitmask |
| recurrence:day_of_month | number | Day of month |
| recurrence:day_of_year | number | Day of year |
| recurrence:occurrences | number | Number of occurrences |
| recurrence:sliding | boolean | Sliding recurrence |
| attachments | string | URI encoded attachments array (returned by upload requests with new=1) |

If sending a `Request={base_64_encoded_json_string}` or a JSON body you can send `{"recurrence":{"start":string,"end":string, ...}}`

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
    "error": string
}
```

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}
```

or

```
PUT /WorldClientAPI/tasks/{folder_id}?session={session_id}
```

---

### Update Task

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id (required) |

**Optional Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| subject | string | Task subject |
| notes | string | Task notes |
| completed | boolean | Completion status |
| completed_date | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| percent_complete | number | Completion percentage (0-100) |
| priority | number | Priority level |
| status | number | Task status |
| private | boolean | Private flag |
| estimated_hours | number | Estimated hours |
| actual_hours | number | Actual hours |
| mileage | string | Mileage information |
| billing | string | Billing information |
| companies | string | Associated companies |
| due | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| categories | string | Comma delimited list |
| reminder | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:start | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:end | string | ISO-8601 Date YYYY-MM-DDTHH:mm:ss+/-HH:mm |
| recurrence:type | number | Recurrence type |
| recurrence:sub_type | number | Recurrence sub-type |
| recurrence:interval | number | Recurrence interval |
| recurrence:instance | number | Instance number |
| recurrence:day_of_week_bit_mask | number | Day of week bitmask |
| recurrence:day_of_month | number | Day of month |
| recurrence:day_of_year | number | Day of year |
| recurrence:occurrences | number | Number of occurrences |
| recurrence:sliding | boolean | Sliding recurrence |

> **Note:** You do not include the attachments array when updating a task.

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}
```

or

```
PUT /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

If sending a `Request={base_64_encoded_json_string}` you can send `{"recurrence":{"start":string,"end":string, ...}}`

**Example Update JSON Request**

```json
{
    "id": 1,
    "subject": "Get Something Done",
    "completed": false,
    "percent_complete": 20,
    "priority": 2,
    "start": "2018-10-02T00:00:00+06:00Z",
    "recurrence": {
        "type": 1,
        "sub_type": 0,
        "interval": 3,
        "start": "2018-10-02T00:00:00+06:00Z",
        "sliding": true
    }
}
```

Also remember to URI encode the base64 string.

```
PUT /WorldClientAPI?session={session_id}&route=tasks&folder=1&request=eyJzdWJqZWN0IjoiR2V0IFNvbWV0aGluZyBEb25lIiwiY29tcGxldGVkIjpmYWxzZSwicGVyY2VudF9jb21wbGV0ZSI6MjAsInByaW9yaXR5IjoyLCJzdGFydCI6IjIwMTgtMTAtMDJUMDA6MDA6MDArMDY6MDBaIiwicmVjdXJyZW5jZSI6eyJ0eXBlIjoxLCJzdWJfdHlwZSI6MCwiaW50ZXJ2YWwiOjMsInN0YXJ0IjoiMjAxOC0xMC0wMlQwMDowMDowMCswNjowMFoiLCJzbGlkaW5nIjp0cnVlfX0%3D
```

---

### Remove Recurrence

To remove the recurrence from a task, send `"recurrence": { "type": 0 }`

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=tasks&folder=1&id=1&recurrence:type=0
```

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
    "error": string
}
```

---

### Complete Task

Set the task to complete or incomplete.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |
| complete | boolean | Completion status |
| percent_complete | number | Optional - if complete=false you can include the percent_complete |

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
    "error": string
}
```

**Example Request**

```
PUT /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}
```

or

```
PUT /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

PUT data:

```json
{
    "complete": boolean,
    "percent_complete": number
}
```

> **Note:** percent_complete must be an unsigned integer

---

## DELETE

### Delete Task

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |

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
    "error": string
}
```

**Example Request**

```
DELETE /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id1,task_id2,etc.}
```

or

```
DELETE /WorldClientAPI/tasks/{folder_id}/task/{task_id}?session={session_id}
```

or

```
DELETE /WorldClientAPI/tasks/{folder_id}/task/ids?session={session_id}&id={task_id1,task_id2,etc.}
```

---

### Delete Occurrence

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |
| occurrence | number | 1 |

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
    "error": string
}
```

**Example Request**

```
DELETE /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}&occurrence=1
```

or

```
DELETE /WorldClientAPI/tasks/{folder_id}/task/{task_id}/occurrence?session={session_id}
```

---

### Delete Attachment

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| id | number | task_id |
| attachment | number | attachment_id |

**Responses**

*success*

```json
{
    "attachments": []
}
```

*failure*

```json
{
    "error": string
}
```

**Example Request**

```
DELETE /WorldClientAPI?session={session_id}&route=tasks&folder={folder_id}&id={task_id}&attachment={attachment_id}
```

or

```
DELETE /WorldClientAPI/tasks/{folder_id}/task/{task_id}/attachment/{attachment_id}?session={session_id}
```

---

## Errors

| Error | Description |
|-------|-------------|
| InvalidParameter | The parameter value returned had an invalid value |
| InadequatePermissions | The attempted action is not permitted on this task for this user |
| InvalidRequest | There were invalid parameters in the request |
