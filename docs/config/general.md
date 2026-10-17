# `[general]`

Core processor behaviour, branding URLs, and per-webhook-type processing toggles.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `locale` | string | `"en"` | Default locale for new users and system messages (e.g. `en`, `fr`, `de`, `it`, `ru`). |
| `default_timezone` | string | `""` | IANA timezone name (e.g. `Europe/London`) used by the profile and summary schedulers for users with no location set (lat/lon `0,0`). Empty = the server's local timezone. Users with a `!location` always use the timezone derived from their coordinates. |
| `alert_minimum_time` | int | `120` | Seconds before expiry inside which alerts will not be generated. |
| `ignore_long_raids` | bool | `false` | Ignore raids/eggs with more than 47 minutes remaining, to reduce raid hour / special event spam. |
| `img_url` | string | uicons URL | Base URL for `{{imgUrl}}` references (uicons repository). |
| `img_url_alt` | string | `""` | Second base URL for `{{imgUrlAlt}}` references. |
| `sticker_url` | string | tgUICONS URL | Base URL for `{{stickerUrl}}` (Telegram webp stickers). |
| `request_shiny_images` | bool | `false` | Request shiny variants of images. |
| `populate_pokestop_name` | bool | `false` | Look up nearby pokestop names in the scanner database (requires [`[database.scanner]`](database.md#databasescanner)). |
| `disabled_commands` | array | `[]` | Commands to disable, e.g. `["lure", "nest"]`. |
| `default_template_name` | string | `"1"` | Default DTS template name/number for new trackings. |
| `dts_dictionary` | table | (none) | Custom key/value pairs made available to every DTS template. See [below](#generaldts_dictionary). |
| `shortlink_provider` | string | `""` | URL shortener used for `<S< >S>` markers in DTS. Only `shlink` is implemented; anything else (including `hideuri` and `yourls`) leaves links unshortened. |
| `shortlink_provider_url` | string | `""` | URL of your Shlink instance. |
| `shortlink_provider_key` | string | `""` | Shlink API key. |
| `shortlink_provider_domain` | string | `""` | Shlink domain override, if your instance serves several. |
| `react_map_url` | string | `""` | ReactMap URL for `{{reactMapUrl}}`. |
| `diadem_url` | string | `""` | Diadem URL for `{{diademUrl}}`. |
| `rdm_url` | string | `""` | RDM map URL for `{{rdmUrl}}` (legacy). |
| `rocket_mad_url` | string | `""` | RocketMAD URL for `{{rocketMadUrl}}` (legacy). |
| `role_check_mode` | string | `"ignore"` | `ignore`, `delete`, or `disable-user`. Case sensitive. See [below](#role_check_mode). |
| `disable_pokemon` | bool | `false` | Disable pokemon webhook processing. |
| `disable_raid` | bool | `false` | Disable raid webhook processing. |
| `disable_invasion` | bool | `false` | Disable invasion processing. |
| `disable_lure` | bool | `false` | Disable lure processing. |
| `disable_quest` | bool | `false` | Disable quest processing. |
| `disable_weather` | bool | `false` | Disable weather processing. |
| `disable_nest` | bool | `false` | Disable nest processing. |
| `disable_gym` | bool | `false` | Disable gym processing. |
| `disable_fort_update` | bool | `false` | Disable fort update processing. |
| `disable_max_battle` | bool | `false` | Disable Max Battle processing. |
| `disable_showcase` | bool | `false` | Disable Showcase (pokestop contest) processing. |
| `disable_pokestop` | bool | `false` | **Deprecated, no effect.** Use `disable_lure`, `disable_invasion` and `disable_quest` instead. Still accepted so older configs parse. |

!!! note "Keys that no longer do anything"
    These keys appear in older configs (and some still appear in `config.example.toml`) but PoracleNG does not read them. They are safe to delete:
    `environment`, `process_confirmed_invasion_lineups`, `disable_unconfirmed_invasion`, `disable_pokestop`, and the `[general.images]` / `[general.stickers]` per-type repository tables.

## `role_check_mode`

- **`ignore`**: log only. Users are not deleted or disabled when required roles are removed.
- **`delete`**: delete the user (and their trackings) from the database when the role is removed.
- **`disable-user`**: disable the user when the role is removed and re-enable when restored. Trackings are kept.

## Sub-tables

### `[general.dts_dictionary]`

Arbitrary key/value pairs exposed to every DTS template. They are the lowest-priority layer of the template view, so they never override a real alert field with the same name.

```toml
[general.dts_dictionary]
communityName = "Springfield PoGo"
rulesUrl = "https://example.com/rules"
```

In a template: `{{communityName}}`.

### `[general.available_languages.<code>]`

Languages users can switch between with `!language`. Each entry sets the registration call word and help word for that language.

```toml
[general.available_languages.en]
poracle = "poracle"
help = "help"

[general.available_languages.de]
poracle = "dasporacle"
help = "hilfe"
```
