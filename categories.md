# Categories

> Category management and color configuration for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Required Parameters](#required-parameters)
- [Color Reference](#color-reference)
- [GET Requests](#get-requests)
  - [Get Categories](#get-categories)
- [PUT Requests](#put-requests)
  - [Update Categories](#update-categories)
- [Category Translations](#category-translations)

---

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `route` | string | Must be `categories` |

---

## Color Reference

The color ID is an index for the following hexadecimal color array that uses the [Outlook standard category colors](https://msdn.microsoft.com/en-us/library/ee203806%28v=exchg.80%29.aspx):

```javascript
[
  "#ffffff", "#e7a1a2", "#f9ba89", "#f7dd8f", "#fcfa90", "#78d168", "#9fdcc9", "#c6d2b0",
  "#9db7e8", "#b5a1e2", "#daaec2", "#dad9dc", "#6b7994", "#bfbfbf", "#6f6f6f", "#4f4f4f",
  "#c11a25", "#e2620d", "#c79930", "#b9b300", "#368f2b", "#329b7a", "#778b45", "#2858a5",
  "#5c3fa3", "#93446b"
]
```

---

## GET Requests

### Get Categories

Retrieve all categories and their color IDs.

**Example Request**

```http
GET /WorldClientAPI?route=categories
```

```http
GET /WorldClientAPI/categories
```

**Success Response**

```json
{
  "category_translations": {
    "{name}": []
  },
  "custom_category_translations": {
    "{name}": []
  },
  "domain_categories_enabled": boolean,
  "can_edit_categories": boolean,
  "categories": [
    {
      "name": string,
      "color": number,
      "uuid": string,
      "readOnly": number
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `category_translations` | object | Standard reverse translations |
| `custom_category_translations` | object | Custom reverse translations |
| `domain_categories_enabled` | boolean | Whether domain categories are enabled |
| `can_edit_categories` | boolean | Whether user can edit categories |
| `categories` | array | Array of category objects |
| `categories[].name` | string | Category name |
| `categories[].color` | number | Color index |
| `categories[].uuid` | string | Unique identifier |
| `categories[].readOnly` | number | Whether category is read-only |

**Permission Combinations**

| domain_categories_enabled | can_edit_categories | Result |
|--------------------------|---------------------|--------|
| `true` | `true` | Mix of read-only and editable categories |
| `true` | `false` | Only read-only categories |
| `false` | `true` | All categories are editable |
| `false` | `false` | No categories returned |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |

---

## PUT Requests

### Update Categories

Update the category list. All categories must be sent back.

**Example Request**

```http
PUT /WorldClientAPI?route=categories
```

```http
PUT /WorldClientAPI/categories
```

**Request Body**

```json
{
  "categories": [
    {
      "name": string,
      "color": number,
      "uuid": string,
      "readOnly": number
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

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `Unauthorized` | Invalid session |
| - | `InadequatePermissions` | User cannot edit categories |

---

## Category Translations

The `category_translations` and `custom_category_translations` arrays are available to reverse translate categories for users who may be in the same organization but use different languages.

Standard translations from English to 29 other languages are available at `WorldClient\HTML\locale\{language_code}.json` as discussed in [Translations](translations.md).

### Reverse Translation Example

**Scenario:** User A (German speaker) has a message with category `Vigtig`. User B (Japanese speaker) shares the same folder.

Since `Vigtig` may not mean anything to the Japanese-speaking user, use reverse translations:

```javascript
// Assuming you have extracted the ja.json file
// and the translation exists
const translate = (str) => {
  return translations[str] || str;
};

// custom_category_translations should take precedence over the defaults
let message_category = "Vigtig";
for (let category in custom_category_translations) {
  if (custom_category_translations[category].indexOf(message_category) !== -1) {
    message_category = translate(category); // result is "重要"
    break;
  }
}
```
