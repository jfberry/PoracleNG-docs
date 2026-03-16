# Weather

PoracleNG can alert users when in-game weather changes affect their tracked pokemon, and can predict upcoming weather using AccuWeather forecasts.

## Weather Change Alerts

Enable alerts when weather changes would affect pokemon that users are tracking:

```toml
[weather]
change_alert = false                     # Enable weather change alerts
show_altered_pokemon = false             # Include affected pokemon in the alert
show_altered_pokemon_static_map = false  # Show affected pokemon on the map
show_altered_pokemon_max_count = 10      # Max pokemon to show per alert
```

When `change_alert` is enabled, users who track weather changes will receive alerts when the weather shifts in their areas.

When `show_altered_pokemon` is enabled, the alert includes a list of tracked pokemon that are affected by the weather change (e.g. pokemon that gain or lose weather boost).

## Weather Inference

The processor can infer current weather conditions from pokemon spawn data by analyzing which pokemon are weather-boosted. This happens automatically and does not require any configuration.

Weather inference provides weather data even when the scanner doesn't send weather webhooks directly.

## AccuWeather Forecast

PoracleNG can predict the next hour's weather using AccuWeather forecasts. This enables proactive alerts about upcoming weather changes:

```toml
[weather]
enable_forecast = false
accuweather_api_keys = ["key1", "key2"]  # API keys (rotated)
accuweather_day_quota = 50               # Max API calls per key per day
forecast_refresh_interval = 8            # Hours between API calls per cell
local_first_fetch_hod = 3               # First fetch hour (local time)
smart_forecast = false                   # Fetch on demand for missing cells
```

### API Keys

AccuWeather has daily API call limits. Provide multiple keys to increase your quota — Poracle rotates through them:

```toml
accuweather_api_keys = ["key1", "key2", "key3"]
accuweather_day_quota = 50               # Calls per key per day
```

### Smart Forecast

When `smart_forecast = true`, the processor will fetch weather data on demand when it encounters a weather cell that doesn't have forecast data, rather than only fetching on a schedule.

## User Commands

Users track weather changes with:

=== "Discord"

    ```
    !weather
    ```

=== "Telegram"

    ```
    /weather
    ```

This subscribes the user to weather change alerts in their tracked areas.
