# `[locale]`

Time, date and address formatting for alert templates.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `timeformat` | string | `"en-gb"` | Locale tag used for time/date formatting. |
| `time` | string | `"LTS"` | Time format token (moment.js style). |
| `date` | string | `"L"` | Date format token (moment.js style). |
| `address_format` | string | `""` | Handlebars template used to build the `{{addr}}` tag. Empty = use the country-idiomatic `formattedAddress` as-is. See below. |
| `address_include_country` | bool | `false` | Append the country name to `formattedAddress`. Leave off for single-country communities; turn on for multi-country audiences. |
| `language` | string | `"en"` | Secondary language for `alt` translation in DTS. Useful for getting English names for web links. |

## `address_format`

`address_format` is compiled once at startup and rendered for each geocoded address. When it is empty (the default), `{{addr}}` is the provider-independent `formattedAddress`, built from the [OpenCage address templates](https://github.com/OpenCageData/address-formatting) for the address's country.

Fields available in the template (all providers):

`formattedAddress`, `displayName` (Nominatim only), `streetName`, `streetNumber`, `suburb`, `neighbourhood`, `city`, `town`, `village`, `county`, `state`, `zipcode`, `country`, `countryCode`, `flag`

As well as all normal DTS helpers, two extra helpers are available:

- `{{coalesce a b c}}`: first non-empty argument.
- `{{compactAddress}}`: the PoracleJS/Photon compact layout, e.g. `Mitte: Friedrichstrasse 42, Berlin`.

```toml
[locale]
address_format = ""                                   # default: country-idiomatic formattedAddress
# address_format = "{{{streetName}}} {{streetNumber}}" # street line only (the old default)
# address_format = "{{{displayName}}}"                 # Nominatim's own display_name
# address_format = "{{compactAddress}}"                # PoracleJS-style compact address
```

!!! note "Changed default"
    Older releases defaulted to `"{{{streetName}}} {{streetNumber}}"`. If you relied on that street-only output, set it explicitly.

See also: [Geocoding](geocoding.md).
