# Authentication

> Session management, two-factor authentication, password recovery, and WebAuthn credentials.

## Navigation

[Home](index.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [Basic Authentication](#basic-authentication)
- [Password Recovery](#password-recovery)
- [Change Password](#change-password)
- [Two Factor Authentication](#two-factor-authentication)
- [Disable Two Factor Authentication](#disable-two-factor-authentication)
- [Two Factor Authentication Setup](#two-factor-authentication-setup)
- [Email Verification Setup](#email-verification-setup)
- [Email Verification Resend Code](#email-verification-resend-code)
- [Logout](#logout)
- [CSRF Token](#csrf-token)
- [Credentials API (WebAuthn)](#credentials-api)
  - [Register Challenge](#register-challenge)
  - [Register](#register)
  - [Authenticate Challenge](#authenticate-challenge)
  - [Authenticate](#authenticate)
- [Important Notes](#important-notes)

---

## Required Parameter

| Parameter | Type | Description |
|-----------|------|-------------|
| `authenticate` | string | Authentication type |

---

## Basic Authentication

If `WorldClient.ini` has `[Session] UseCSRFToken=Yes`, request `login_token` in `logon_settings` and send a `data` string variable with it (`WorldClientAPI/settings/logon_settings=login_token&data={some_uuid}`). This creates an HTTP-only, SameSite Strict cookie valid for one use.

### POST `/WorldClientAPI/authenticate/basic`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `basic` |
| `user` | string | Yes | User email address |
| `password` | string | Yes | User password |
| `remember_user` | boolean | No | Enable "remember me" (requires `RememberUserEnabled=Yes`) |
| `terms_of_use_acknowledged` | boolean | No | Accept terms of use (required if `TermsOfUseRequired=Yes`) |
| `data` | string | No | UUID for CSRF (required when `CSRFToken=Yes`) |

**Example Request**

```http
POST /WorldClientAPI?authenticate=Basic&user={user}&password={password}&remember_user=true
```

```http
POST /WorldClientAPI/authenticate/basic
Content-Type: application/x-www-form-urlencoded

user={user}&password={password}&remember_user=true&terms_of_use_acknowledged=true
```

```http
POST /WorldClientAPI/authenticate/basic
Content-Type: application/json

{
  "user": "{user}",
  "password": "{password}",
  "remember_user": true,
  "terms_of_use_acknowledged": true
}
```

**Success Response** — `200 OK`

```json
{
  "authed": boolean,
  "session": string,
  "csrf_token": string,
  "user": string,
  "remembered": boolean
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authed` | boolean | Always `true` on success |
| `session` | string | Session ID for subsequent requests |
| `csrf_token` | string | Base64 token for POST/PUT/DELETE requests |
| `user` | string | User's actual email address |
| `remembered` | boolean | Whether "remember me" is active |

**Success Response — Two Factor Required** — `200 OK`

User needs to enter a one-time password to complete authentication.

```json
{
  "authed": false,
  "warning": "TwoFactorAuthRequired",
  "token": string,
  "user": string,
  "partial": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authed` | boolean | Always `false` |
| `warning` | string | `TwoFactorAuthRequired` |
| `token` | string | Token for 2FA request |
| `user` | string | User's actual email address |
| `partial` | string | Partially obfuscated email address where code is sent |

**Success Response — 2FA Setup Required** — `200 OK`

User needs to set up Two Factor Authentication before logging in.

```json
{
  "authed": true,
  "session": string,
  "csrf_token": string,
  "warning": "TwoFactorAuthSetupRequired",
  "token": string,
  "user": string
}
```

**Success Response — Password Expired** — `200 OK`

User needs to change their password before logging in.

```json
{
  "authed": false,
  "warning": "OldPassword",
  "token": string
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 404 | Not Found | Frozen user, disabled user, etc. |
| 400 | `InvalidUserOrPassword` | Invalid credentials |
| 400 | `MissingParameter` | Required `user` or `password` missing |
| 400 | `MustAcceptTermsOfUse` | Terms not acknowledged when required |

---

## Password Recovery

This endpoint is only available if `PasswordRecoveryEnabled` is enabled for the domain and the user has set up a recovery email address.

This is a two-step process.

### Step One — Request Recovery Email

#### GET `/WorldClientAPI/authenticate/recovery`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `recovery` |
| `user` | string | Yes | User email address |

**Example Request**

```http
GET /WorldClientAPI?authenticate=recovery&user={user}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

> Always responds with success to avoid revealing user existence. If a recovery email exists, the email is sent.

### Step Two — Complete Recovery

If an email is sent, a link is provided to the user's recovery email with the signature:

```
http://mail.example.com/WorldClient.dll?View=PasswordRecovery&User={user}&RecoveryToken={recovery_token}
```

The `recovery_token` is required to change the password. The remainder of this feature is handled with WorldClient Templates.

---

## Change Password

If a user's password has expired, they can change it by entering their old password.

### POST `/WorldClientAPI/authenticate/changepassword`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `ChangePassword` |
| `user` | string | Yes | User email address |
| `old_password` | string | Yes | Current password |
| `new_password` | string | Yes | New password |
| `confirm_password` | string | Yes | New password confirmation |
| `token` | string | Yes | Token from login attempt or 2FA verification |

**Example Request**

```http
POST /WorldClientAPI?authenticate=ChangePassword
Content-Type: application/x-www-form-urlencoded

user={user}&token={token}&old_password={old}&new_password={new}&confirm_password={new}
```

```http
POST /WorldClientAPI/authenticate/changepassword
Content-Type: application/json

{
  "user": "{user}",
  "token": "{token}",
  "old_password": "{old_password}",
  "new_password": "{new_password}",
  "confirm_password": "{confirm_password}"
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

The user must now login with the new password.

**Error Responses**

| Status | Description |
|--------|-------------|
| 404 | Invalid request (no details provided for security) |

---

## Two Factor Authentication

### Request Verification Code

#### GET `/WorldClientAPI/authenticate/2fa`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FA` |
| `user` | string | Yes | User email address |
| `send_code` | number | Yes | Must be `1` |
| `token` | string | Yes | Token from basic authentication response |

**Example Request**

```http
GET /WorldClientAPI?authenticate=2FA&user={user}&token={token}&send_code=1
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidParameter` | User cannot receive a code (`parameter: "send_code"`) |

### Verify One-Time Password

#### POST `/WorldClientAPI/authenticate/2fa`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FA` |
| `user` | string | Yes | User email address |
| `one_time_password` | string | Yes | 6-digit code from authenticator or email |
| `token` | string | Yes | Token from basic authentication response |
| `remember_user` | boolean | No | Enable "remember me" |

**Example Request**

```http
POST /WorldClientAPI?authenticate=2FA
Content-Type: application/x-www-form-urlencoded

user={user}&token={token}&one_time_password={otp}
```

```http
POST /WorldClientAPI/authenticate/2fa
Content-Type: application/json

{
  "user": "{user}",
  "token": "{token}",
  "one_time_password": "{one_time_password}"
}
```

**Success Response** — `200 OK`

```json
{
  "authed": true,
  "session": string,
  "csrf_token": string,
  "user": string,
  "remembered": boolean
}
```

**Success Response — Password Change Required** — `200 OK`

```json
{
  "authed": false,
  "warning": "OldPassword",
  "user": string,
  "token": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `token` | string | New token for change password request |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `AuthRefused` | Invalid one-time password |

---

## Disable Two Factor Authentication

If a user has lost their Two Factor Authentication device, they can either:
- Send an email to the administrator requesting 2FA be disabled
- Receive an email to their recovery email to disable it themselves

The user must have authenticated with their password before using this feature.

### Get Recovery Email Status

#### GET `/WorldClientAPI/authenticate/lost_2fa`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `lost_2fa` |
| `user` | string | Yes | User email address |
| `token` | string | Yes | Token from basic authentication |

**Example Request**

```http
GET /WorldClientAPI?authenticate=lost_2fa&user={user}&token={token}
```

```http
GET /WorldClientAPI/authenticate/lost_2fa?user={user}&token={token}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "partial": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `partial` | string | Partial recovery email (empty if none exists) |

**Error Responses**

| Status | Description |
|--------|-------------|
| 404 | Bad token or user |

### Request 2FA Disable

#### POST `/WorldClientAPI/authenticate/lost_2fa`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `lost_2fa` |
| `user` | string | Yes | User email address |
| `password` | string | Yes | User password |
| `token` | string | Yes | Token from basic authentication |
| `recovery_email` | string | Conditional | Required if user has a recovery email |

If the user has a `recovery_email`, it is required. Otherwise, an email is sent to the administrator.

**Example Request**

```http
POST /WorldClientAPI?authenticate=lost_2fa
Content-Type: application/x-www-form-urlencoded

user={user}&token={token}&password={password}&recovery_email={recovery_email}
```

```http
POST /WorldClientAPI/authenticate/lost_2fa
Content-Type: application/json

{
  "user": "{user}",
  "token": "{token}",
  "password": "{password}",
  "recovery_email": "{recovery_email}"
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidRecoveryEmail` | Incorrect recovery email |
| 404 | Not Found | Invalid request |

---

## Two Factor Authentication Setup

HTTPS is required for these requests.

If requiring users to use Two Factor Authentication, this route enables setup. It requires two calls: one to get the secret, another to verify pairing.

### Get Secret

#### GET `/WorldClientAPI/authenticate/2fasetup/get_secret`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FASetup` |
| `session` | string | Yes | Session ID |
| `token` | string | Yes | Token from basic authentication |
| `get_secret` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?authenticate=2FASetup&session={session_id}&token={token}&get_secret=1
```

```http
GET /WorldClientAPI/authenticate/2fasetup/get_secret?session={session_id}&token={token}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "secret": string
}
```

To use a QR code, encode a link in the following format:

```
otpauth://totp/{domain}:{user}?secret={secret}&issuer={domain}
```

> Remember to URI encode the colon.

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidSession` | Session variable is invalid |
| 400 | `InvalidToken` | Token variable is invalid |
| 400 | `InternalError` | Server error creating user secret |
| 400 | `InvalidParameter` | Invalid parameter (e.g., `otp_email`) |
| 404 | Not Found | Not HTTPS, etc. |

---

## Email Verification Setup

HTTPS is required. `AllowTwoFactorAuthEmail` must be enabled in `WorldClient\Domains.ini`.

This route requires users to have a secondary email address (not an alias). It requires two calls: one to set the secret and send a code, another to verify.

### Send Verification Email

#### GET `/WorldClientAPI/authenticate/2fasetup/send_email`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FASetup` |
| `session` | string | Yes | Session ID |
| `token` | string | Yes | Token from basic authentication |
| `send_email` | number | Yes | Must be `1` |
| `otp_email` | string | Yes | Secondary email address |

**Example Request**

```http
GET /WorldClientAPI?authenticate=2FASetup&session={session_id}&token={token}&send_email=1&otp_email={email}
```

```http
GET /WorldClientAPI/authenticate/2fasetup/send_email?session={session_id}&token={token}&otp_email={email}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidParameter` | Invalid `otp_email` |
| 404 | Not Found | Not HTTPS, etc. |

### Verify Email Code

#### POST `/WorldClientAPI/authenticate/2fasetup`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FASetup` |
| `session` | string | Yes | Session ID |
| `token` | string | Yes | Token from basic authentication |
| `verify_email` | number | Yes | Must be `1` |
| `one_time_password` | string | Yes | 6-digit verification code |

**Example Request**

```http
POST /WorldClientAPI?authenticate=2FASetup&session={session_id}
Content-Type: application/x-www-form-urlencoded

token={token}&one_time_password={code}&verify_email=1
```

```http
POST /WorldClientAPI/authenticate/2fasetup?session={session_id}
Content-Type: application/json

{
  "token": "{token}",
  "one_time_password": "{code}",
  "verify_email": 1
}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidSession` | Session variable is invalid |
| 400 | `InvalidToken` | Token variable is invalid |
| 400 | `PairingFailed` | One-time password doesn't match |
| 400 | `InvalidParameter` | Invalid parameter value |
| 404 | Not Found | Not HTTPS, etc. |

---

## Email Verification Resend Code

HTTPS is required. `AllowTwoFactorAuthEmail` must be enabled.

If the user doesn't receive an email, they can request a new one.

### GET `/WorldClientAPI/authenticate/2fasetup`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `2FASetup` |
| `session` | string | Yes | Session ID |
| `token` | string | Yes | Token from basic authentication |
| `resend_code` | number | Yes | Must be `1` |

**Example Request**

```http
GET /WorldClientAPI?authenticate=2FASetup&session={session_id}&token={token}&resend_code=1
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "otp_email": string
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `Invalid email address` | No valid email configured |
| 401 | Unauthorized | Authentication failed |
| 404 | Not Found | Not HTTPS, etc. |

---

## Logout

### POST `/WorldClientAPI/authenticate/logout`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `logout` |
| `session` | string | Yes | Session ID |

**Example Request**

```http
POST /WorldClientAPI?authenticate=logout&session={session_id}
```

```http
POST /WorldClientAPI/authenticate/logout
Content-Type: application/x-www-form-urlencoded

session={session_id}
```

**Success Response** — `200 OK`

```json
{
  "success": true
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `InvalidRequest` | Invalid request |

---

## CSRF Token

For additional security, enable CSRF Tokens in `MDaemon\WorldClient\WorldClient.ini` under `[Session]` by setting `UseCSRFTokens=Yes` or using MDaemon Remote Administration.

When enabled, any authentication response with a session ID also includes a `csrf_token` property. This value must be included in all `POST`, `PUT`, and `DELETE` requests.

When a new session is established, a fresh `csrf_token` is issued. Each token is only valid for the session it was issued with.

### Including the CSRF Token

**Method 1 — Form hidden field:**

```html
<input type="hidden" name="CSRFToken" value="{csrf_token}" />
```

**Method 2 — HTTP Header:**

```http
X-CSRF-Token: {csrf_token}
```

For `GET` or `OPTIONS` requests, the token is ignored.

An empty or invalid token results in a `403 Forbidden` response.

---

## Credentials API

WebAuthn biometric authentication support.

To enable this feature, set `EnableWorldClientCredentialsAPI=Yes` in `MDaemon\WorldClient\Domains.ini`.

This implementation requires the user to authenticate with their password before registering a biometric credential. When the user's password expires, biometric credentials are deleted and must be recreated.

**References:**
- [Web Authentication API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API)
- [W3C WebAuthn Specification](https://w3c.github.io/webauthn/)

---

### Register Challenge

Registration can only occur after authentication. For convenience, registration should occur immediately after login if possible.

The response should be passed to `navigator.credentials.create` after converting `challenge`, `user.id`, and `excludeCredentials[i].id` to `Uint8Array`.

#### GET `/WorldClientAPI/authenticate/credentials/register/challenge`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `credentials` |
| `register` | number | Yes | Must be `1` |
| `challenge` | number | Yes | Must be `1` |
| `use` | string | Yes | `login` or `2fa` |
| `session` | string | Yes | Session ID |

**Example Request**

```http
GET /WorldClientAPI?authenticate=credentials&session={session_id}&register=1&challenge=1&use=login
```

```http
GET /WorldClientAPI/authenticate/credentials/register/challenge?use=2fa
```

**Success Response** — `200 OK`

```json
{
  "challenge": string,
  "user": {
    "id": string,
    "name": string,
    "displayName": string
  },
  "rp": {
    "name": string,
    "id": string
  },
  "timeout": number,
  "attestation": string,
  "pubkeyCredParams": [
    { "type": "public-key", "alg": number }
  ],
  "authenticationSelection": {
    "residentKey": string,
    "requireResidentKey": boolean,
    "userVerification": string
  },
  "excludeCredentials": [
    {
      "id": string,
      "transports": string[],
      "type": string
    }
  ],
  "extensions": array
}
```

| Field | Type | Description |
|-------|------|-------------|
| `challenge` | string | Base64-encoded cryptographic random number (convert to Uint8Array) |
| `user.id` | string | Base64-encoded user ID (convert to Uint8Array) |
| `user.name` | string | User email address |
| `user.displayName` | string | User display name |
| `rp.name` | string | Relying party name |
| `rp.id` | string | Host name |
| `timeout` | number | Timeout in milliseconds (120000 = 2 minutes) |
| `attestation` | string | Attestation type (`none`) |
| `pubkeyCredParams` | array | Supported algorithms (ES256, ES384, ES512, RS256, RS384, RS512, PS256, PS384, PS512) |
| `excludeCredentials` | array | Existing credentials to exclude (IDs need Uint8Array conversion) |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `Unknown webauthn error` | Check server logs for details |

### Helper Function: base64ToBuffer

```javascript
function base64ToBuffer(base64String) {
  const str = atob(base64String);
  const buffer = new ArrayBuffer(str.length);
  const byteView = new Uint8Array(buffer);
  for (let i = 0; i < str.length; i++) {
    byteView[i] = str.charCodeAt(i);
  }
  return buffer;
}
```

---

### Register

`navigator.credentials.create` returns a Promise that resolves to an `AuthenticatorResponse` (`attestation`) containing an `AuthenticatorAttestationResponse`:

```json
{
  "authenticatorAttachment": "platform",
  "id": string,
  "rawId": ArrayBuffer,
  "response": object,
  "type": "public-key"
}
```

The `attestation.id` and `attestation.response` properties need conversion before being sent to the server. Since they cannot be changed directly, copy all properties to a new object.

#### POST `/WorldClientAPI/authenticate/credentials/register`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `credentials` |
| `session` | string | Yes | Session ID |
| `register` | number | Yes | Must be `1` |

**Request Body**

```json
{
  "authenticatorAttachment": string,
  "type": string,
  "credentialId": string,
  "response": {
    "attestationObject": string,
    "clientDataJSON": string
  },
  "clientExtentionResults": object,
  "transports": string[],
  "useType": string
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authenticatorAttachment` | string | `platform` or `cross-platform` |
| `type` | string | Should be `public-key` |
| `credentialId` | string | Base64 (converted from base64url `attestation.id`) |
| `response.attestationObject` | string | Base64-encoded attestation object |
| `response.clientDataJSON` | string | Base64-encoded client data JSON |
| `clientExtentionResults` | object | Result of `attestation.getClientExtensionResults()` |
| `transports` | array | Result of `attestation.response.getTransports()` |
| `useType` | string | `login` or `2fa` |

**Example Request**

```http
POST /WorldClientAPI?authenticate=credentials&session={session_id}&register=1
Content-Type: application/json

{
  "authenticatorAttachment": "platform",
  "type": "public-key",
  "credentialId": "{base64_credential_id}",
  "response": {
    "attestationObject": "{base64_attestation}",
    "clientDataJSON": "{base64_client_data}"
  },
  "clientExtentionResults": {},
  "transports": ["internal"],
  "useType": "login"
}
```

**Success Response** — `200 OK`

```json
{
  "success": true,
  "user": string,
  "useType": string
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `RegistrationFailed` | Check server logs for details |

### Helper Function: bufferToBase64

```javascript
function bufferToBase64(buffer) {
  const byteView = new Uint8Array(buffer);
  let str = "";
  for (const charCode of byteView) {
    str += String.fromCharCode(charCode);
  }
  return byteEncode(str); // Use byte encoding, not atob
}
```

---

### Authenticate Challenge

Similar to registration, start by requesting a challenge, then call `navigator.credentials.get`, and send the response to the server.

Convert base64-encoded IDs to Uint8Arrays before passing to `navigator.credentials.get`:

```javascript
challenge.challenge = base64ToBuffer(challenge.challenge);
challenge.allowCredentials = challenge.allowCredentials.map(credential => {
  credential.id = base64ToBuffer(credential.id);
  return credential;
});

navigator.credentials.get({ publicKey: challenge });
```

#### POST `/WorldClientAPI/authenticate/credentials/auth/challenge`

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `authenticate` | string | Yes | Must be `credentials` |
| `session` | string | Yes | Session ID |
| `auth` | number | Yes | Must be `1` |
| `challenge` | number | Yes | Must be `1` |
| `useType` | string | Yes | `login` or `2fa` |
| `token` | string | Conditional | Required if `useType` is `2fa` |

**Request Body**

```json
{
  "user": string,
  "useType": string,
  "token": string
}
```

**Example Request**

```http
POST /WorldClientAPI?authenticate=credentials&session={session_id}&auth=1&challenge=1
Content-Type: application/json

{
  "user": "{user}",
  "useType": "login"
}
```

**Success Response** — `200 OK`

```json
{
  "challenge": string,
  "timeout": number,
  "rpId": string,
  "allowCredentials": [
    {
      "id": string,
      "transports": string[],
      "type": string
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `challenge` | string | Base64-encoded challenge (convert to Uint8Array) |
| `timeout` | number | Timeout in milliseconds (120000) |
| `rpId` | string | Relying party ID (domain) |
| `allowCredentials` | array | Allowed credentials (IDs need Uint8Array conversion) |

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 400 | `Unknown webauthn error` | Check server logs for details |

---

### Authenticate

Take the results from `navigator.credentials.get({ publicKey: challenge })` (`assertion`) and add the following properties:

```json
{
  "user": string,
  "lang": string,
  "theme": string,
  "remember_user": boolean,
  "useType": string,
  "token": string
}
```

Convert all ArrayBuffers in `assertion.response` to base64, and convert `assertion.id` from base64url to base64.

#### POST `/WorldClientAPI/authenticate/credentials/auth`

**Request Body**

```json
{
  "authenticatorAttachment": string,
  "credentialId": string,
  "response": {
    "authenticatorData": string,
    "clientDataJSON": string,
    "signature": string,
    "userHandle": string
  },
  "type": string,
  "clientExtensionResults": object,
  "user": string,
  "lang": string,
  "theme": string,
  "remember_user": boolean,
  "useType": string
}
```

**Example Request**

```http
POST /WorldClientAPI?authenticate=credentials&session={session_id}&auth=1
Content-Type: application/json

{
  "authenticatorAttachment": "platform",
  "credentialId": "{base64_credential_id}",
  "response": {
    "authenticatorData": "{base64_auth_data}",
    "clientDataJSON": "{base64_client_data}",
    "signature": "{base64_signature}",
    "userHandle": "{base64_user_handle}"
  },
  "type": "public-key",
  "clientExtensionResults": {},
  "user": "{user}",
  "lang": "en",
  "theme": "default",
  "remember_user": false,
  "useType": "login"
}
```

**Success Response** — `200 OK`

```json
{
  "authed": true,
  "session": string,
  "csrf_token": string,
  "user": string,
  "remembered": boolean
}
```

**Success Response — Two Factor Required** — `200 OK`

```json
{
  "authed": false,
  "warning": "TwoFactorAuthRequired",
  "token": string,
  "user": string,
  "partial": string
}
```

**Success Response — 2FA Setup Required** — `200 OK`

```json
{
  "authed": true,
  "session": string,
  "csrf_token": string,
  "warning": "TwoFactorAuthSetupRequired",
  "token": string,
  "user": string
}
```

**Error Responses**

| Status | Error | Description |
|--------|-------|-------------|
| 401 | `AuthenticationFailed` | Authentication failed |

---

## Important Notes

### Alternative POST Format

All POSTs can use base64-encoded JSON:

```http
POST /WorldClientAPI?authenticate=2FASetup&session={session_id}&request={base64_encoded_json}
```

Example JSON before encoding:

```json
{
  "token": "{token}",
  "get_secret": 1
}
```

Encoded: `request=eyJ0b2tlbiI6e3Rva2VufSwiZ2V0U2VjcmV0IjogMX0=`

> The `request` parameter can be part of form data. Be sure to URI encode it.

### Authorization Header

Once authenticated, pass the session in the `Authorization` header:

```http
Authorization: Bearer {session_id}
```

### Remembered Users

A remembered user receives additional values when their session expires:

```json
{
  "error": "InvalidSession",
  "new_session": string,
  "csrf_token": string,
  "user": string,
  "remembered": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `new_session` | string | New session ID to use |
| `csrf_token` | string | New CSRF token |
| `user` | string | Non-alias user address |
| `remembered` | boolean | Always `true` |

Update the `session_id` being sent to the `new_session` returned above.

This is only returned on `authenticate=basic` requests to prevent multiple requests from breaking the remember me functionality.
