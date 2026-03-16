# Config File Reference

Complete reference for `config/config.toml`. All settings are shown with their default values.

!!! tip
    You only need to include settings you want to override. See `config/config.example.toml` in the PoracleNG repository for the full annotated reference.

## Processor

Network settings for the Go processor component.

```toml
[processor]
host = "0.0.0.0"                        # Listen address (0.0.0.0 = all interfaces)
port = 3030                              # Listen port (receives webhooks from scanner)
alerter_url = "http://localhost:3031"    # URL where the alerter is listening
ip_whitelist = []                        # Restrict webhook sources (empty = allow all)
```

## Alerter

Network settings for the Node.js alerter component.

```toml
[alerter]
host = "127.0.0.1"                      # Listen address
port = 3031                              # Listen port
ip_whitelist = []                        # Restrict access (empty = allow all)
ip_blacklist = []                        # Block specific IPs
api_secret = ""                          # API secret for PoracleWeb (blank = API disabled)
processor_url = "http://localhost:3030"  # URL where the processor is listening
```

!!! note
    The `api_secret` must be set for external API access (e.g. PoracleWeb). Leave blank to disable the API.

## Database

Database connection for PoracleNG's own data (users, tracking rules, etc.).

```toml
[database]
host = "127.0.0.1"
port = 3306
user = "poracleuser"
password = "poraclepassword"
database = "poracle"
# scanner_type = "golbat"               # golbat (default) or rdm
```

### Scanner Database

If using RDM for quest/pokestop name lookups, configure the scanner database:

```toml
[database.scanner]
host = "127.0.0.1"
port = 3306
user = "scanneruser"
password = "scannerpassword"
database = "scannerdb"
```

## Geofence

See [Geofences](geofence.md) for detailed configuration.

```toml
[geofence]
paths = ["geofences/geofence.json"]     # Geofence file paths
default_name = "city"                    # Default fence name
default_color = "#3399ff"               # Default fence color for maps

[geofence.koji]
bearer_token = ""                        # Koji API token for geofence downloads
cache_dir = ".cache/geofences"           # Cache directory for downloaded geofences
```

## PVP

PVP ranking calculation settings.

```toml
[pvp]
level_caps = [50]                        # Level caps for rank calculations (e.g. [50, 51])
include_mega_evolution = false           # Include mega evolutions in rankings
evolution_direct_tracking = false        # Track PVP evolutions directly (e.g. Vaporeon matches Eevee)
filter_by_track = false                  # Auto-filter PVP listings by user's track requirements

# Minimums on track command to prevent overly broad tracking
filter_max_rank = 10
filter_great_min_cp = 1400
filter_ultra_min_cp = 2350
filter_little_min_cp = 450

# Display thresholds passed into DTS
display_max_rank = 10
display_great_min_cp = 1400
display_ultra_min_cp = 2350
display_little_min_cp = 450
```

## Weather

See [Weather](weather.md) for detailed configuration.

```toml
[weather]
change_alert = false                     # Enable weather change alerts
show_altered_pokemon = false             # Show weather-affected pokemon in alerts
show_altered_pokemon_static_map = false
show_altered_pokemon_max_count = 10

# AccuWeather forecast
enable_forecast = false
accuweather_api_keys = []
accuweather_day_quota = 50
forecast_refresh_interval = 8            # Hours between API calls per cell
local_first_fetch_hod = 3               # First fetch hour of day (local time)
smart_forecast = false                   # Pull on demand for missing cells
```

## Area Security

See [Area Security](areasecurity.md) for detailed configuration.

```toml
[area_security]
enabled = false
strict_locations = false                 # Check alerts against location_fence
```

## Locale

Date, time, and language formatting.

```toml
[locale]
timeformat = "en-gb"
time = "LTS"
date = "L"
address_format = "{{{streetName}}} {{streetNumber}}"
language = "en"                          # Secondary language for 'alt' translations in DTS
```

## General

General application settings.

```toml
[general]
environment = "production"
alert_minimum_time = 120                 # Seconds before expiry to suppress alerts
ignore_long_raids = false                # Ignore raids > 47min (reduce event spam)
img_url = "https://raw.githubusercontent.com/nileplumb/PkmnShuffleMap/master/UICONS"
img_url_alt = ""
sticker_url = "https://raw.githubusercontent.com/bbdoc/tgUICONS/main/Shuffle"
request_shiny_images = false
populate_pokestop_name = false           # [RDM] Lookup pokestop names in scanner DB
locale = "en"                            # Default locale (en, fr, de, it, ru, etc.)
default_template_name = "1"              # Default DTS template for new trackings
shortlink_provider = "hideuri"           # hideuri, shlink, or yourls
shortlink_provider_url = ""
shortlink_provider_key = ""
shortlink_provider_domain = ""
rdm_url = ""                             # URL for RDM map links in DTS
react_map_url = ""                       # URL for ReactMap links in DTS
rocket_mad_url = ""                      # URL for RocketMAD links in DTS
```

### Role Check Mode

Controls what happens when a user loses their Discord role or Telegram group membership:

```toml
role_check_mode = "ignore"
# "ignore"       - Log and don't take action
# "delete"       - Delete user and all trackings
# "disable-user" - Disable user (re-enable when restored), trackings preserved
```

### Disable Webhook Types

Selectively disable processing of specific webhook types:

```toml
disable_pokemon = false
disable_raid = false
disable_pokestop = false                 # Also disables invasions for RDM/Golbat
disable_invasion = false
disable_lure = false
disable_quest = false
disable_weather = false
disable_nest = false
disable_gym = false
disable_fort_update = false
process_confirmed_invasion_lineups = false
disable_unconfirmed_invasion = false
```

### Disabled Commands

Prevent specific commands from being used:

```toml
# disabled_commands = ["track", "Pokemon"]
```

### Available Languages

Configure languages users can switch between:

```toml
# [general.available_languages.en]
# poracle = "poracle"
# help = "help"
# [general.available_languages.de]
# poracle = "dasporacle"
# help = "hilfe"
```

## Geocoding

See [Geocoding](geocoding.md) and [Static Maps](staticmaps.md) for detailed configuration.

```toml
[geocoding]
provider = "none"                        # none, nominatim, or google
provider_url = ""
forward_only = false
cache_detail = 3
static_provider = "none"                 # none, tileservercache, google, osm, or mapbox
static_provider_url = ""
geocoding_key = [""]                     # Google API keys (array, rotated)
static_key = [""]                        # Map tile keys (array, rotated)
width = 320
height = 200
zoom = 15
```

## Tuning

Performance tuning parameters.

!!! warning
    Do not change these settings without consulting the Poracle community on Discord.

```toml
[tuning]
# Processor
reload_interval_secs = 60               # How often to reload tracking data from DB
encounter_cache_ttl = 3600               # Seconds to cache encounter data
worker_pool_size = 4                     # Number of matching worker goroutines
batch_size = 50                          # Matched results per batch to alerter
flush_interval_millis = 100              # Max time before flushing partial batch

# Alerter
max_database_connections = 15            # Max DB connections per worker
concurrent_matched_processors = 10       # Concurrent matched payload processors
matched_max_queue_size = 5000            # Max buffered items before backpressure
concurrent_discord_destinations = 10     # Concurrent Discord sends per bot
concurrent_telegram_destinations = 10    # Concurrent Telegram sends per bot
concurrent_discord_webhooks = 10         # Concurrent Discord webhook sends
```

## Alert Limits

Rate limiting to prevent individual users or channels from overwhelming the system.

```toml
[alert_limits]
timing_period = 240                      # Seconds over which limits are calculated
dm_limit = 20                            # Max messages per user per period
channel_limit = 40                       # Max messages per channel per period
max_limits_before_stop = 10              # Times hitting limit before user is stopped
disable_on_stop = false                  # Admin disable (vs just stop)
shame_channel = ""                       # Discord channel to report stopped users
```

### Limit Overrides

Override limits for specific channels, users, or webhooks:

```toml
# [[alert_limits.overrides]]
# target = "channel_or_user_id"
# limit = 100

# [[alert_limits.overrides]]
# target = "webhook_name"
# limit = 200
```

## Tracking

Restrictions on user tracking commands.

```toml
[tracking]
everything_flag_permissions = "allow-any"
# "allow-any"                     - Unrestricted, recorded as wildcard
# "allow-and-always-individually" - Unrestricted, recorded as individual rows
# "allow-and-ignore-individually" - Unrestricted, users can't opt for individual rows
# "deny"                          - Users must track individual pokemon

default_distance = 0                     # Default distance for distance-only tracking
max_distance = 0                         # Max tracking circle radius (0 = no limit)
enable_gym_battle = false                # Allow battle_changes option in !gym
default_user_tracking_level_cap = 0      # Default PVP level cap (0 = all)
```

## Reconciliation

Automated user and channel sync settings.

```toml
[reconciliation.discord]
update_user_names = false
remove_invalid_users = true
register_new_users = false
update_channel_names = true
update_channel_notes = false
unregister_missing_channels = false

[reconciliation.telegram]
update_user_names = false
remove_invalid_users = true
```

## Stats

Rarity and shiny statistics calculation.

```toml
[stats]
min_sample_size = 10000                  # Min sightings before calculating rarity
window_hours = 8                         # Rolling window for stats
refresh_interval_mins = 5                # Recalculation interval
rarity_group_2_uncommon = 1.0            # % threshold for uncommon
rarity_group_3_rare = 0.5               # % threshold for rare
rarity_group_4_very_rare = 0.03          # % threshold for very rare
rarity_group_5_ultra_rare = 0.01         # % threshold for ultra rare
```

## Fallbacks

Fallback image URLs used when normal image sources fail.

```toml
[fallbacks]
static_map = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/staticMap.png"
img_url = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/mon.png"
img_url_weather = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/weather.png"
img_url_egg = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/uni.png"
img_url_gym = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/gym.png"
img_url_pokestop = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/pokestop.png"
pokestop_url = "https://raw.githubusercontent.com/KartulUdus/PoracleJS/images/fallback/pokestop.png"
```

## Logging

```toml
[logging]
level = "verbose"                        # silly, debug, verbose, info, warn
timing_stats = false                     # Elevate timing stats to verbose level
max_age = 7                              # Days to keep log files
max_size = 50                            # Max log file size in MB
max_backups = 5                          # Old log files to keep
compress = false                         # Gzip compress rotated logs
webhook_log_limit = 12                   # Hours to keep hourly webhook logs

[logging.enable_logs]
webhooks = false                         # Matched webhook log (can be large)
discord = true                           # Discord message delivery
telegram = true                          # Telegram message delivery
pvp = false                              # PVP calculations (verbose)

# Raw webhook logging (processor only)
[webhookLogging]
enabled = false
filename = "logs/webhooks.log"
max_size = 100
max_age = 1
max_backups = 3
compress = false
```
