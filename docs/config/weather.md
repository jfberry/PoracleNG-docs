# `[weather]`

Weather change alerts and AccuWeather forecast integration for predicting boost changes.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `change_alert` | bool | `false` | Enable weather change alerts. |
| `show_altered_pokemon` | bool | `false` | Track weather-changed pokemon to surface in DTS. |
| `show_altered_pokemon_static_map` | bool | `false` | Show weather-changed pokemon on static maps. |
| `show_altered_pokemon_max_count` | int | `10` | Max number of altered pokemon listed per alert. |
| `enable_inference` | bool | `true` | Infer cell weather from observed pokemon boosts. |
| `enable_forecast` | bool | `false` | Enable AccuWeather forecast lookups. |
| `accuweather_api_keys` | array(string) | `[]` | AccuWeather keys; Poracle rotates through them. |
| `accuweather_day_quota` | int | `50` | Max API calls per key per day. |
| `forecast_refresh_interval` | int | `8` | Hours between API calls per cell. |
| `local_first_fetch_hod` | int | `3` | Local hour-of-day for first fetch (e.g. `3` = 3am). |
| `smart_forecast` | bool | `false` | Pull forecast on demand if no data exists for a cell. |

The forecast feature predicts the next hour of weather to detect boost changes before they happen. AccuWeather keys are rotated automatically across the array.

See also: [Weather configuration guide](../configuration/weather.md).
