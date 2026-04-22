# `[locale]`

Time, date and address formatting for alert templates.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `timeformat` | string | `"en-gb"` | Locale tag used for time/date formatting. |
| `time` | string | `"LTS"` | Time format token (moment.js style). |
| `date` | string | `"L"` | Date format token (moment.js style). |
| `address_format` | string | `"{{{streetName}}} {{streetNumber}}"` | How addresses are constructed for the `{{addr}}` tag. |
| `language` | string | `"en"` | Secondary language for `alt` translation in DTS — useful for retrieving English detail for web links. |
