# `[general]`

Core processor behaviour, branding URLs, and per-webhook-type processing toggles.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `environment` | string | `"production"` | Leave as `production`. |
| `alert_minimum_time` | int | `120` | Seconds before expiry inside which alerts will not be generated. |
| `ignore_long_raids` | bool | `false` | Ignore raids > 47m to reduce raid hour / special event spam. |
| `img_url` | string | uicons URL | Base URL for `{{imgUrl}}` references (uicons repository). |
| `img_url_alt` | string | `""` | Second base URL for `{{imgUrlAlt}}` references. |
| `sticker_url` | string | tgUICONS URL | Base URL for `{{stickerUrl}}` (Telegram webp stickers). |
| `request_shiny_images` | bool | `false` | Request shiny variants of images. |
| `populate_pokestop_name` | bool | `false` | (RDM) lookup nearby pokestop names in scanner DB. |
| `locale` | string | `"en"` | Default locale (e.g. `en`, `fr`, `de`, `it`, `ru`). |
| `disabled_commands` | array | `[]` | Commands to disable, e.g. `["track", "Pokemon"]`. |
| `default_template_name` | string | `"1"` | Default template name/number for new trackings. |
| `shortlink_provider` | string | `"hideuri"` | Supported: `hideuri`, `shlink`, `yourls`. |
| `shortlink_provider_url` | string | `""` | URL for self-hosted shortlink provider. |
| `shortlink_provider_key` | string | `""` | API key for shortlink provider. |
| `shortlink_provider_domain` | string | `""` | Domain to use, if supported by provider. |
| `rdm_url` | string | `""` | RDM map URL for DTS shortlinks. |
| `react_map_url` | string | `""` | Reactmap URL for DTS shortlinks. |
| `rocket_mad_url` | string | `""` | RocketMAD URL for DTS shortlinks. |
| `role_check_mode` | string | `"ignore"` | `ignore`, `delete`, or `disable-user`. Case sensitive. |
| `disable_pokemon` | bool | `false` | Disable pokemon webhook processing. |
| `disable_raid` | bool | `false` | Disable raid webhook processing. |
| `disable_pokestop` | bool | `false` | Disable pokestop processing (also disables invasion for RDM/Golbat). |
| `disable_invasion` | bool | `false` | Disable invasion processing. |
| `disable_lure` | bool | `false` | Disable lure processing. |
| `disable_quest` | bool | `false` | Disable quest processing. |
| `disable_weather` | bool | `false` | Disable weather processing. |
| `disable_nest` | bool | `false` | Disable nest processing. |
| `disable_gym` | bool | `false` | Disable gym processing. |
| `disable_fort_update` | bool | `false` | Disable fort update processing. |
| `disable_max_battle` | bool | `false` | Disable MAX battle processing. |
| `process_confirmed_invasion_lineups` | bool | `false` | Process confirmed invasion lineups. |
| `disable_unconfirmed_invasion` | bool | `false` | Skip unconfirmed invasions. |

## `role_check_mode`

- **`ignore`** — log only; do not delete or disable users when required roles are removed.
- **`delete`** — delete user (and their trackings) from database when role is removed.
- **`disable-user`** — disable user when role is removed; re-enable when restored. Trackings are preserved.

## Sub-tables

### `[general.images]` / `[general.stickers]`

Override the uicons / sticker repository per alert type. Keys: `monster`, `gym`, `nest`, `invasion`, `lure`, `quest`, `raid`, `weather`.

```toml
[general.images]
monster = "https://example.com/uicons"
```

### `[general.available_languages.<code>]`

Languages users can switch between with `!language`. Each entry sets the call word and help text.

```toml
[general.available_languages.en]
poracle = "poracle"
help = "help"

[general.available_languages.de]
poracle = "dasporacle"
help = "hilfe"
```
