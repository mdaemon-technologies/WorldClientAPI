# Settings

Manage user settings, preferences, autoresponder, IMAP filters, POP accounts, app passwords, two-factor authentication, and email verification.

---

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Table of Contents

### GET Requests
- [Logon Settings](#logon-settings)
- [Custom Buttons](#custom-buttons)
- [Login History](#login-history)
- [Autoresponder Settings](#autoresponder-settings)
- [IMAP Filters](#imap-filters)
- [POP Accounts](#pop-accounts)
- [App Passwords](#app-passwords)
- [Setup Two Factor Auth](#setup-two-factor-auth)
- [Set up Email Verification](#email-verification-setup)
- [Email Verification Resend Code](#email-verification-resend-code)
- [Specific Settings](#specific-settings)

### POST Requests
- [Autoresponder Settings (POST)](#autoresponder-settings-post)
- [New IMAP Filter](#new-imap-filter)
- [Move IMAP Filter Up](#move-imap-filter-up)
- [Move IMAP Filter Down](#move-imap-filter-down)
- [New POP Account](#new-pop-account)
- [Verify Two Factor Auth Pairing](#verify-two-factor-auth-pairing)
- [Cancel Two Factor Auth Setup](#cancel-two-factor-auth-setup)
- [Disable Two Factor Auth](#disable-two-factor-auth)
- [Verify Email Verification Pairing](#verify-email-verification-pairing)
- [Cancel Email Verification Setup](#cancel-email-verification-setup)
- [Change Verification Code Email](#change-verification-code-email)
- [Disable Email Verification](#disable-email-verification)
- [Disable Remember Me](#disable-remember-me)
- [Change Recovery Email](#change-recovery-email)
- [Change Password](#change-password)
- [Create App Password](#create-app-password)
- [Add Alias Display Name](#add-alias-display-name)

### PUT Requests
- [Update IMAP Filters](#update-imap-filters)
- [Update POP Accounts](#update-pop-accounts)
- [Rename App Password](#rename-app-password)
- [Edit Alias Display Name](#edit-alias-display-name)
- [Update Settings](#update-settings)

### DELETE Requests
- [IMAP Filter](#delete-imap-filter)
- [POP Account](#delete-pop-account)
- [Revoke App Password](#revoke-app-password)
- [Delete Alias Display Name](#delete-alias-display-name)

---

## Required URI Parameter

```
route=settings
```

---

## GET Requests

### Logon Settings

This request will output server settings that do not require authentication, and cannot be changed via the API.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `logon_settings` | string | Comma delimited list of settings or JSON array (URI encoded) |

**Possible Values**

| Setting | Type | Description |
|---------|------|-------------|
| `admin_contact_info` | string | Can contain HTML |
| `custom_logo` | boolean | Custom logo in use |
| `languages` | array | Array of objects: `[{ "value": "en", "name": "English" }, ...]` |
| `language` | string | Default language for the domain |
| `logo_banner` | string | Small logo URL for `<img src="{logo_banner}" />` |
| `logo_large` | string | Large logo URL for `<img src="{logo_large}" />` |
| `hide_login_lang` | boolean | Hide the login language select |
| `hide_login_theme` | boolean | Hide the login theme select |
| `remember_user_enabled` | boolean | Domain allows users to be remembered |
| `terms_of_use_required` | boolean | Domain requires the terms of use to be agreed to by the user before login |
| `terms_of_use` | string | The terms of use set by the domain |
| `themes` | array | Array of strings: `["Pro", "LookOut", "WorldClient", ...]` |
| `theme` | string | Default theme for the domain |
| `update_available` | boolean | Is there a new version of MDaemon available |
| `version` | string | The current version of MDaemon |

**Response (Success)**

```json
{
  "{setting_name}": "{setting_value}"
}
```

**Response (Failure)**

```json
{
  "error": "{errorMessage}"
}
```

**Example Request**

```http
GET /WorldClientAPI?route=settings&logon_settings=setting1,setting2,setting3

GET /WorldClientAPI/settings?logon_settings=setting1,setting2,setting3
```

---

### Specific Settings

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `settings` | string | Comma delimited list of settings or JSON array (URI encoded) |

**Response (Success)**

```json
{
  "settings": [
    {
      "name": "string",
      "value": "string"
    }
  ]
}
```

**Response (Failure)**

```json
{
  "error": "{errorMessage}"
}
```

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&settings=setting1,setting2,setting3

GET /WorldClientAPI/settings?session={session_id}&settings=setting1,setting2,setting3
```

---

### Custom Buttons

A maximum of 8 custom buttons are allowed to be defined on the server. This request retrieves the `text`, `link` URL, and `image` URL for each button.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `custom_buttons` | number | Set to `1` to retrieve custom buttons |

**Response (Success)**

```json
{
  "custom_buttons": [
    {
      "text": "string",
      "link": "string",
      "image": "string"
    }
  ]
}
```

**Response (Failure)**

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&custom_buttons=1

GET /WorldClientAPI/settings?session={session_id}&custom_buttons=1
```

---

### Login History

If logged in, you can retrieve the login history for the last 30 user logins.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `login_history` | number | Set to `1` to retrieve login history |

**Response (Success)**

```json
{
  "logins": [
    {
      "ip": "string",
      "agent": "string",
      "country": "string",
      "time": "string"
    }
  ]
}
```

> **Note:** The `time` field is an ISO-8601 Date in format `YYYY-MM-DDTHH:mm:ss+/-HH:mm`

**Response (Failure)**

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&login_history=1

GET /WorldClientAPI/settings?session={session_id}&login_history=1
```

---

### Autoresponder Settings

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `auto_responder` | number | Set to `1` to retrieve autoresponder settings |

**Response (Success)**

```json
{
  "enabled": true,
  "schedule": true,
  "days": {
    "sunday": true,
    "monday": true,
    "tuesday": true,
    "wednesday": true,
    "thursday": true,
    "friday": true,
    "saturday": true
  },
  "start": "string",
  "end": "string",
  "text": "string"
}
```

> **Note:** The `start` and `end` fields are ISO-8601 Dates in format `YYYY-MM-DDTHH:mm+/-HH:mm`

**Response (Failure)**

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&auto_responder=1

GET /WorldClientAPI/settings?session={session_id}&auto_responder=1
```

---

### IMAP Filters

IMAP Filters are built into MDaemon for the purpose of filtering incoming messages and some users have access to edit their own filters. To check for access send a settings `GET` request with `CanEditIMAPFilters`:

```http
GET /WorldClientAPI/settings?session={session_id}&settings=CanEditIMAPFilters
```

**Logic Property Values**

| Value | Description |
|-------|-------------|
| `0` | No additional conditions |
| `1` | All conditions being true, take the specified action |
| `2` | Any conditions being true, take the specified action |

**Action Property Values**

| Value | Description |
|-------|-------------|
| `forward` | Used in conjunction with the `email` property |
| `redirect` | Used in conjunction with the `email` property |
| `move` | Used in conjunction with the `folder` property |
| `delete` | Delete the message |

The `email` property specifies an email address to either `redirect` or `forward` a message to.

The `folder` property specifies the folder path of a folder to `move` a message to.

The `conditions` array lists the conditions to be applied.

**Condition `what` Property Values**

| Value | Description |
|-------|-------------|
| `message size` | A special condition property which allows different match options from the message header properties |
| `to` | To header |
| `cc` | CC header |
| `from` | From header |
| `subject` | Subject header |
| `sender` | Sender header |
| `x-mdmailing-list` | Mailing list header |
| `x-mdrcpt-to` | Recipient header |
| `x-mddnsbl-result` | DNSBL result header |
| `x-spam-flag` | Spam flag header |
| *custom header* | Any custom header |

**Condition `match` Property Values**

| Value | Description | Restrictions |
|-------|-------------|--------------|
| `1` | Equals | - |
| `2` | Does not equal | - |
| `3` | Contains | Cannot be used with "message size" |
| `4` | Does not contain | Cannot be used with "message size" |
| `5` | Starts with | Cannot be used with "message size" |
| `6` | Ends with | Cannot be used with "message size" |
| `7` | Exists | Cannot be used with "message size" |
| `8` | Does not exist | Cannot be used with "message size" |
| `9` | Is greater than | Can only be used with "message size" |
| `10` | Is less than | Can only be used with "message size" |

The `literal` condition property is a user specified string value to compare with the `what` property.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` to retrieve IMAP filters |

**Response (Success)**

```json
{
  "max_conditions": 10,
  "filters": [
    {
      "id": 1,
      "logic": 0,
      "action": "move",
      "email": "string",
      "folder": "string",
      "conditions": [
        {
          "what": "from",
          "match": 3,
          "literal": "string"
        }
      ]
    }
  ]
}
```

**Response (Failure)**

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&imap_filters=1

GET /WorldClientAPI/settings?session={session_id}&imap_filters=1
```

---

### POP Accounts

If `MultiPOPServerEnabled` returns true, users can add POP accounts for MDaemon to retrieve messages from.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pop_accounts` | number | Set to `1` to retrieve POP accounts |

**Response (Success)**

```json
{
  "accounts": [
    {
      "id": 1,
      "host": "gmail.com",
      "user_name": "string",
      "password": true,
      "enabled": true,
      "remove_mail": true,
      "use_apop": false,
      "use_oauth": true,
      "can_use_oauth": true,
      "oauth_authorized": true
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | The index + 1 of the account |
| `host` | string | The hostname, e.g. gmail.com |
| `user_name` | string | The username for the given host |
| `password` | boolean | Whether or not the user has designated a password |
| `enabled` | boolean | Enable this POP account |
| `remove_mail` | boolean | Delete retrieved messages from the POP account |
| `use_apop` | boolean | APOP encrypts passwords when making POP requests |
| `use_oauth` | boolean | OAuth 2.0 available for accounts |
| `can_use_oauth` | boolean | Is OAuth available for the account requested |
| `oauth_authorized` | boolean | OAuth is successfully setup for the account |

**Response (Failure)**

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&pop_accounts=1

GET /WorldClientAPI/settings?session={session_id}&pop_accounts=1
```

---

### App Passwords

If `CanEditAppPasswords` returns true and the protocol is `https`, users can add App Passwords.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `app_passwords` | number | Set to `1` to retrieve app passwords |

**Response (Success)**

```json
{
  "appPasswords": [
    {
      "id": "string",
      "name": "string",
      "created": "string",
      "lastUsed": "string",
      "lastIp": "string"
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | UUID v4 |
| `name` | string | The name designated by the user |
| `created` | string | RFC822 Date |
| `lastUsed` | string | RFC822 Date |
| `lastIp` | string | Last IP address used |

**Response (Failure)**

```json
{
  "error": "Call failed"
}
```

or

```json
{
  "error": "Access denied"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&app_passwords=1

GET /WorldClientAPI/settings?session={session_id}&app_passwords=1
```

---

### Setup Two Factor Auth

`HTTPS` is required for this feature. This allows you to get the current secret, or to get a new secret for an authenticator app.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `get_secret` | string | `new` for a new secret, `current` for existing secret |
| `otp_email` | string | (Optional) Email address to send two factor auth verification codes to |

**Response (Success)**

```json
{
  "success": true,
  "secret": "string"
}
```

**Response (Failure)**

```json
{
  "error": "InvalidPassword"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&get_secret=new&current_password={current_password}

GET /WorldClientAPI?route=settings&session={session_id}&get_secret=current&current_password={current_password}

GET /WorldClientAPI/settings?session={session_id}&get_secret=new&current_password={current_password}

GET /WorldClientAPI/settings?session={session_id}&get_secret=current&current_password={current_password}
```

---

### Email Verification Setup

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`. This allows you to setup a secondary email address for verifying your identity.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `send_email` | number | Set to `1` to send verification email |
| `otp_email` | string | Email address to send two factor auth verification codes to |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidPassword"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&send_email=1&current_password={current_password}&otp_email={otp_email}

GET /WorldClientAPI/settings?session={session_id}&send_email=1&current_password={current_password}&otp_email={otp_email}
```

---

### Email Verification Resend Code

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`. This allows you to resend a code to an email address that has already been designated. This request will fail if there is no one time password email address setup.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `resend_code` | number | Set to `1` to resend verification code |

**Response (Success)**

```json
{
  "success": true,
  "otp_email": "string"
}
```

> **Note:** The `otp_email` field contains the address the new code was sent to.

**Response (Failure)**

```json
{
  "error": "Invalid email address"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
GET /WorldClientAPI?route=settings&session={session_id}&resend_code=1

GET /WorldClientAPI/settings?session={session_id}&resend_code=1
```

---

## POST Requests

### Autoresponder Settings (POST)

Send a JSON Autoresponder object to update the autoresponder settings.

**Request Body**

```json
{
  "enabled": true,
  "schedule": true,
  "days": {
    "sunday": true,
    "monday": true,
    "tuesday": true,
    "wednesday": true,
    "thursday": true,
    "friday": true,
    "saturday": true
  },
  "start": "string",
  "end": "string",
  "text": "string"
}
```

> **Note:** The `start` and `end` fields are ISO-8601 Dates in format `YYYY-MM-DDTHH:mm+/-HH:mm`

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `auto_responder` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&auto_responder=1

POST /WorldClientAPI/settings?session={session_id}&auto_responder=1
```

---

### New IMAP Filter

Create a new IMAP filter that will be added to the end of the filter list.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` |

**Request Body**

```json
{
  "new_filter": {
    "logic": 0,
    "action": "move",
    "email": "string",
    "folder": "string",
    "conditions": [
      {
        "what": "from",
        "match": 3,
        "literal": "string"
      }
    ]
  }
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&imap_filters=1

POST /WorldClientAPI/settings?session={session_id}&imap_filters=1
```

---

### Move IMAP Filter Up

Move an IMAP filter up in the list.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` |
| `up` | number | Set to `1` |
| `id` | number | The filter ID to move |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&imap_filters=1&up=1&id={id}

POST /WorldClientAPI/settings?session={session_id}&imap_filters=1&up=1&id={id}
```

---

### Move IMAP Filter Down

Move an IMAP filter down in the list.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` |
| `down` | number | Set to `1` |
| `id` | number | The filter ID to move |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&imap_filters=1&down=1&id={id}

POST /WorldClientAPI/settings?session={session_id}&imap_filters=1&down=1&id={id}
```

---

### New POP Account

Create a new POP account for MDaemon to collect mail from.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pop_accounts` | number | Set to `1` |

**Request Body**

```json
{
  "new_account": {
    "host": "string",
    "user_name": "string",
    "password": "string",
    "enabled": true,
    "remove_mail": true,
    "use_apop": false,
    "use_oauth": true
  }
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&pop_accounts=1

POST /WorldClientAPI/settings?session={session_id}&pop_accounts=1
```

---

### Verify Two Factor Auth Pairing

The `one_time_password` is the six digit code retrieved from the user's authenticator application.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `verify_pairing` | string | The one time password from the authenticator app |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&verify_pairing={one_time_password}

POST /WorldClientAPI/settings?session={session_id}&verify_pairing={one_time_password}
```

---

### Cancel Two Factor Auth Setup

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `reset_secret` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&reset_secret=1

POST /WorldClientAPI/settings?session={session_id}&reset_secret=1
```

---

### Disable Two Factor Auth

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `disable_2fa` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&disable_2fa=1&current_password={current_password}

POST /WorldClientAPI/settings?session={session_id}&disable_2fa=1&current_password={current_password}
```

---

### Verify Email Verification Pairing

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`. The `one_time_password` is the six digit code retrieved from the user's secondary email address.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `verify_email_pairing` | string | The one time password from the secondary email |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&verify_email_pairing={one_time_password}

POST /WorldClientAPI/settings?session={session_id}&verify_email_pairing={one_time_password}
```

---

### Cancel Email Verification Setup

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `reset_email` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&reset_email=1

POST /WorldClientAPI/settings?session={session_id}&reset_email=1
```

---

### Change Verification Code Email

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `change_otp_email` | number | Set to `1` |
| `current_password` | string | The user's current password |
| `otp_email` | string | The new email address for verification codes |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "Invalid email address"
}
```

or

```json
{
  "error": "Access denied"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&change_otp_email=1&current_password={current_password}&otp_email={otp_email}

POST /WorldClientAPI/settings?session={session_id}&change_otp_email=1&current_password={current_password}&otp_email={otp_email}
```

---

### Disable Email Verification

`HTTPS` is required for this feature. `AllowTwoFactorAuthEmail` must be turned on in the `WorldClient\Domains.ini`.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `disable_otp_email` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&disable_otp_email=1&current_password={current_password}

POST /WorldClientAPI/settings?session={session_id}&disable_otp_email=1&current_password={current_password}
```

---

### Disable Remember Me

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `disable_remember_me` | number | Set to `1` |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&disable_remember_me=1

POST /WorldClientAPI/settings?session={session_id}&disable_remember_me=1
```

---

### Change Recovery Email

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `recovery_email` | string | The new recovery email address |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&current_password={current_password}&recovery_email={recovery_email}

POST /WorldClientAPI/settings?session={session_id}&current_password={current_password}&recovery_email={recovery_email}
```

---

### Change Password

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `current_password` | string | The user's current password |
| `new_password` | string | The new password |
| `confirm_password` | string | Confirmation of the new password |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&current_password={current_password}&new_password={new_password}&confirm_password={confirm_password}

POST /WorldClientAPI/settings?session={session_id}&current_password={current_password}&new_password={new_password}&confirm_password={confirm_password}
```

---

### Create App Password

This will create an App Password and output it to the user this one and only time. After it has been output here it cannot be retrieved. This request cannot be completed without `https`. The user's current password must be included.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `app_passwords` | number | Set to `1` |
| `name` | string | The name for the app password |
| `current_password` | string | The user's current password |

**Request Body**

```json
{
  "name": "string",
  "current_password": "string"
}
```

**Response (Success)**

```json
{
  "success": true,
  "password": "string",
  "name": "string",
  "id": "string",
  "created": "string"
}
```

> **Note:** The `created` field is an RFC822 Date.

**Response (Failure)**

```json
{
  "error": "HTTPS required"
}
```

or

```json
{
  "error": "Incorrect password."
}
```

or

```json
{
  "error": "Name required"
}
```

or

```json
{
  "error": "Call failed"
}
```

or

```json
{
  "error": "Access denied"
}
```

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&app_passwords=1

POST /WorldClientAPI/settings?session={session_id}&app_passwords=1
```

---

### Add Alias Display Name

This will create a different display name for the given alias `email` address. This only applies to aliases, not the default account address. `AllowEditAliasDisplayName=Yes` is required in the User settings to use this feature.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `alias_displayname` | number | Set to `1` |
| `email` | string | The alias email address |
| `display_name` | string | The display name for the alias |

**Request Body**

```json
{
  "email": "string",
  "display_name": "string"
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InadequatePermissions"
}
```

**Example Request**

```http
POST /WorldClientAPI?route=settings&session={session_id}&alias_displayname=1

POST /WorldClientAPI/settings?session={session_id}&alias_displayname=1
```

---

## PUT Requests

### Update IMAP Filters

Send all IMAP Filters back to the server.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` |

**Request Body**

```json
{
  "filters": [
    {
      "id": 1,
      "logic": 0,
      "action": "move",
      "email": "string",
      "folder": "string",
      "conditions": [
        {
          "what": "from",
          "match": 3,
          "literal": "string"
        }
      ]
    }
  ]
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "{errorMessage}"
}
```

**Example Request**

```http
PUT /WorldClientAPI?route=settings&session={session_id}&imap_filters=1

PUT /WorldClientAPI/settings?session={session_id}&imap_filters=1
```

---

### Update POP Accounts

Send all POP Accounts back to the server.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pop_accounts` | number | Set to `1` |

**Request Body**

```json
{
  "accounts": [
    {
      "host": "string",
      "user_name": "string",
      "password": "string",
      "enabled": true,
      "remove_mail": true,
      "use_apop": false,
      "use_oauth": true
    }
  ]
}
```

> **Note:** If you send a boolean for `password`, the existing password will not be changed, whereas any string will change the password.

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "{errorMessage}"
}
```

**Example Request**

```http
PUT /WorldClientAPI?route=settings&session={session_id}&pop_accounts=1

PUT /WorldClientAPI/settings?session={session_id}&pop_accounts=1
```

---

### Rename App Password

Rename an App Password.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `app_passwords` | number | Set to `1` |
| `id` | string | The UUID v4 of the app password |
| `name` | string | The new name |

**Request Body**

```json
{
  "id": "string",
  "name": "string"
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "ID required"
}
```

or

```json
{
  "error": "NewName required"
}
```

or

```json
{
  "error": "Call failed"
}
```

or

```json
{
  "error": "Access denied"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
PUT /WorldClientAPI?route=settings&session={session_id}&app_passwords=1

PUT /WorldClientAPI/settings?session={session_id}&app_passwords=1
```

---

### Edit Alias Display Name

This will edit the display name for the given alias `email` address. This only applies to aliases, not the default account address.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `alias_displayname` | number | Set to `1` |
| `email` | string | The alias email address |
| `display_name` | string | The new display name |

**Request Body**

```json
{
  "email": "string",
  "display_name": "string"
}
```

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InadequatePermissions"
}
```

**Example Request**

```http
PUT /WorldClientAPI?route=settings&session={session_id}&alias_displayname=1

PUT /WorldClientAPI/settings?session={session_id}&alias_displayname=1
```

---

### Update Settings

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `{setting1}` | string | Value for setting1 |
| `{setting2}` | string | Value for setting2 |
| `{setting3}` | string | Value for setting3 |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "{errorMessage}"
}
```

**Example Request**

```http
PUT /WorldClientAPI?route=settings&session={session_id}&{setting1}={value1}&{setting2}={value2}

PUT /WorldClientAPI/settings?session={session_id}&{setting1}={value1}&{setting2}={value2}
```

---

## DELETE Requests

### Delete IMAP Filter

Delete an IMAP filter from the list.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `imap_filters` | number | Set to `1` |
| `id` | number | The filter ID to delete |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
DELETE /WorldClientAPI?route=settings&session={session_id}&imap_filters=1&id={id}

DELETE /WorldClientAPI/settings?session={session_id}&imap_filters=1&id={id}
```

---

### Delete POP Account

Delete a POP Account from the list.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pop_accounts` | number | Set to `1` |
| `id` | number | The account ID to delete |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InvalidRequest"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
DELETE /WorldClientAPI?route=settings&session={session_id}&pop_accounts=1&id={id}

DELETE /WorldClientAPI/settings?session={session_id}&pop_accounts=1&id={id}
```

---

### Revoke App Password

Revoke an App Password. After this action the password will no longer work.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `app_passwords` | number | Set to `1` |
| `id` | string | The UUID v4 of the app password to revoke |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "Id required"
}
```

or

```json
{
  "error": "Call failed"
}
```

or

```json
{
  "error": "Access denied"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
DELETE /WorldClientAPI?route=settings&session={session_id}&app_passwords=1&id={id}

DELETE /WorldClientAPI/settings?session={session_id}&app_passwords=1&id={id}
```

---

### Delete Alias Display Name

This will delete the display name that was created for the given alias `email` address. This only applies to aliases, not the default account address.

**Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `alias_displayname` | number | Set to `1` |
| `email` | string | The alias email address |

**Response (Success)**

```json
{
  "success": true
}
```

**Response (Failure)**

```json
{
  "error": "InadequatePermissions"
}
```

- `401 Unauthorized`
- `404 Not Found`

**Example Request**

```http
DELETE /WorldClientAPI?route=settings&session={session_id}&alias_displayname=1&email={email}

DELETE /WorldClientAPI/settings?session={session_id}&alias_displayname=1&email={email}
```

---

## Error Messages

| Error | Description |
|-------|-------------|
| `InvalidParameter` | The parameter value returned had an invalid value |
| `InadequatePermissions` | The attempted action is not permitted on this folder for this user |
