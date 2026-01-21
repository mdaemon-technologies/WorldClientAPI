# Translations

> Language support and localization for MDaemon Webmail.

## Navigation

[Home](README.md) | [Authenticate](authenticate.md) | [Settings](settings.md) | [Folders](folders.md) | [Compose](compose.md) | [Messages](messages.md) | [Contacts](contacts.md) | [Events](events.md) | [Tasks](tasks.md) | [Notes](notes.md) | [Documents](documents.md) | [RTC](rtc.md) | [Categories](categories.md) | [Reminders](reminders.md) | [Recorder](recorder.md) | [FreeBusy](freebusy.md) | [Cloud Storage](cloud-storage.md) | [Conversations](conversations.md) | [Ping](ping.md)

---

## Contents

- [JSON Translations](#json-translations)
- [Languages](#languages)
- [Browser Code Conversions](#browser-code-conversions)

---

## JSON Translations

Translations are a UTF-8 encoded resource available at `/locale/{language_code}.json`.

The base language is US English, so there is no `en.json` file.

As of MDaemon 19, there are over 2700 strings translated into 29 languages.

**Translation File Format**

```json
{
  "version": string,
  "language": string,
  "translations": {
    "{string to translate}": "{translation}"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `version` | string | Version of MDaemon |
| `language` | string | Language code |
| `translations` | object | Key-value pairs of translations |

**Important:** All quotes are HTML encoded, and some translations are `plain/html`. To convert the HTML quotes back to `"`:

```javascript
const decodeQuotes = (str) => {
  return str.replace(/&quot;/ig, "\"");
};
```

**Example Translation Method**

```javascript
const translate = (str) => {
  return decodeQuotes(translations[str] || str);
};
```

**Example Request**

```http
GET /locale/{language_code}.json
```

---

## Languages

The language codes do not perfectly follow the [browser language code standard](https://www.metamodpro.com/browser-language-codes). Below are the available languages and their MDaemon Webmail language codes.

| Language | Code | Language | Code |
|----------|------|----------|------|
| Arabic | ar | Italian | it |
| Catalan | ca | Japanese | ja |
| Chinese | zh | Korean | ko |
| Chinese (Taiwan) | tw | Norwegian (Bokmal) | no |
| Czech | cs | Polish | pl |
| Danish | da | Portuguese (Brazil) | pt |
| Dutch | nl | Romanian | ro |
| English (United Kingdom) | uk | Russian | ru |
| French Canadian | fc | Serbian | sr |
| Finnish | fi | Spanish | es |
| French | fr | Swedish | sv |
| German | de | Thai | th |
| Greek | gr | Turkish | tr |
| Hungarian | hu | Vietnamese | vi |
| Indonesian | id | | |

---

## Browser Code Conversions

To get the current language of a browser:

```javascript
const browserCode = navigator.userLanguage ? navigator.userLanguage : navigator.language;
```

**Browser Language Code to MDaemon Webmail Code Conversion**

```javascript
const browserToMDCodeConverter = {
  "en-US": "en",
  "en-GB": "uk",
  "en-AU": "uk",
  "en-NZ": "uk",
  "en-CA": "uk",
  "en-ZA": "uk",
  "fr-FR": "fr",
  "fr-CA": "fc",
  "fr-CH": "fr",
  "zh-CN": "zh",
  "zh-TW": "tw",
  "de-AT": "de",
  "de-DE": "de",
  "de-LI": "de",
  "de-CH": "de",
  "pt-BR": "pt",
  "pt-PT": "pt",
  "it-IT": "it",
  "it-CH": "it",
  "nb": "no",
  "es-419": "es",
  "sh": "sr"
};
```

**Example Usage**

```javascript
const lang = browserToMDCodeConverter[browserCode] || browserCode;

fetch(`/locale/${lang}.json`)
  .then(response => response.json())
  .then(data => {
    // Use translations
  });
```
