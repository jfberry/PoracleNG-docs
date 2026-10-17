# Config File Reference

Overview of `config/config.toml`. Each section below shows common settings with their default values and links to the full field reference under **Config Reference**.

!!! tip
    You only need to include settings you want to override; defaults are built in. See `config/config.example.toml` in the PoracleNG repository for an annotated example, or use the **[Poracle Config UI](config-ui.md)** to edit your config in a browser.

!!! note "`overrides.json` is gone"
    Older installs stored web-editor changes in `config/overrides.json`. On startup PoracleNG merges that file into `config.toml` once and then stops using it. `config.toml` is the only config file.

## Processor

HTTP listener for scanner webhooks and the API. PoracleNG is a single Go binary, so this is the only network section. Full reference: [`[processor]`](../config/processor.md).

```toml
[processor]
host = "127.0.0.1"                       # 127.0.0.1 = this machine only; 0.0.0.0 = all interfaces (Docker sets 0.0.0.0 via PORACLE_HOST)
port = 3030                              # Receives webhooks and serves the API
ip_whitelist = []                        # IPs/CIDRs allowed on ALL routes (empty = allow all; CIDR 5.3+)
ip_blacklist = []                        # IPs/CIDRs refused on all routes
trusted_proxies = []                     # 5.3+: reverse proxies whose X-Forwarded-For is believed
api_secret = ""                          # X-Poracle-Secret for /api/* — blank = NO AUTHENTICATION
enable_pprof = false                     # 5.3+: serve /debug/pprof behind api_secret
```

!!! danger "Blank `api_secret` means an open API"
    If `api_secret` is empty, `/api/*` accepts requests **without any authentication**. Anyone who can reach the port can read and change users, tracking rules, config and templates. Always set a long random secret, which PoracleWeb and the Config UI also need. `POST /`, `GET /health` and `GET /metrics` are never authenticated. In 5.2.x `/debug/pprof` is also always open; from 5.3 it only exists with `enable_pprof = true` and sits behind `api_secret`. From 5.3, startup warns when the secret is blank and the listen host isn't loopback (including Docker's `0.0.0.0`). Older configs that set `api_secret` under `[alerter]` are still read as a fallback; move it to `[processor]`.

## Database

PoracleNG's own database, plus an optional scanner database connection. Full reference: [`[database]`](../config/database.md).

```toml
[database]
host = "127.0.0.1"
port = 3306
user = "poracleuser"
password = "poraclepassword"
database = "poracle"

[database.scanner]                       # optional: nearby stops on maps, gym/stop names, gym: lookups
host = "127.0.0.1"
port = 3306
user = "scanneruser"
password = "scannerpassword"
database = "scannerdb"
type = "golbat"                          # "golbat" (default) or "rdm"
```

## Geofence

See [Geofences](geofence.md) and [`[geofence]`](../config/geofence.md).

```toml
[geofence]
paths = ["geofences/geofence.json"]     # Geofence files or URLs
default_name = "city"                    # Prefix for unnamed fences (city1, city2, ...)

[geofence.koji]
bearer_token = ""                        # Koji API token for geofence downloads
cache_dir = ".cache/geofences"           # Cache for downloaded geofences (relative to config/)
```

## PVP

Full reference: [`[pvp]`](../config/pvp.md).

```toml
[pvp]
level_caps = [50]                        # Must match Golbat's PVP level caps
include_mega_evolution = false           # Default for rules without a mega keyword
evolution_direct_tracking = false
filter_by_track = false
filter_max_rank = 100                    # Worst rank a user may track (5.2.x example shipped 10)
filter_great_min_cp = 1400
filter_ultra_min_cp = 2350
filter_little_min_cp = 450
display_max_rank = 10
display_great_min_cp = 1400
display_ultra_min_cp = 2350
display_little_min_cp = 450
```

## Weather

See [Weather](weather.md) and [`[weather]`](../config/weather.md).

```toml
[weather]
change_alert = false
show_altered_pokemon = false
show_altered_pokemon_static_map = false
show_altered_pokemon_max_count = 10
enable_inference = true

enable_forecast = false
accuweather_api_keys = []
accuweather_day_quota = 500              # Calls per key per day
forecast_refresh_interval = 8
local_first_fetch_hod = 3
smart_forecast = false
```

## Area Security

See [Area Security](areasecurity.md) and [`[area_security]`](../config/area_security.md).

```toml
[area_security]
enabled = false
strict_locations = false
```

## Locale

Full reference: [`[locale]`](../config/locale.md).

```toml
[locale]
timeformat = "en-gb"
time = "LTS"
date = "L"
address_format = ""                      # Empty = country-idiomatic formattedAddress
address_include_country = false
language = "en"                          # Secondary language for 'alt' translations in DTS
```

## General

Full reference: [`[general]`](../config/general.md).

```toml
[general]
locale = "en"
#default_timezone = "Europe/London"      # For users with no location set
alert_minimum_time = 120
ignore_long_raids = false
img_url = "https://raw.githubusercontent.com/nileplumb/PkmnShuffleMap/master/UICONS"
img_url_alt = ""
sticker_url = "https://raw.githubusercontent.com/bbdoc/tgUICONS/main/Shuffle"
request_shiny_images = false
populate_pokestop_name = false           # Needs [database.scanner]
default_template_name = "1"
role_check_mode = "ignore"               # ignore, delete, disable-user
shortlink_provider = ""                  # Only "shlink" is implemented
shortlink_provider_url = ""
shortlink_provider_key = ""
shortlink_provider_domain = ""
react_map_url = ""
diadem_url = ""
rdm_url = ""
rocket_mad_url = ""
# disabled_commands = ["lure", "nest"]

# Webhook type switches
disable_pokemon = false
disable_raid = false
disable_invasion = false
disable_lure = false
disable_quest = false
disable_weather = false
disable_nest = false
disable_gym = false
disable_fort_update = false
disable_max_battle = false
disable_showcase = false
```

`environment`, `disable_pokestop`, `process_confirmed_invasion_lineups`, `disable_unconfirmed_invasion`, `[general.images]` and `[general.stickers]` have no effect and can be removed.

## Discord

See [Discord](discord.md) and [`[discord]`](../config/discord.md).

```toml
[discord]
enabled = true
token = "bot-token"                      # Delivery bot (only the first token is used)
#command_token = ""                      # Optional separate command bot
guilds = ["guild-id"]
channels = ["channel-id"]
user_role = []
admins = ["user-id"]
prefix = "!"
admin_channel_id = ""                    # Operator notices
thread_keep_alive_interval_hours = 24    # Built-in default is 0 (off) if omitted

# All [discord.*] sub-tables must come AFTER the plain [discord] keys
[discord.slash_commands]
enabled = false
register_globally = false
guilds = []
```

## Telegram

See [Telegram](telegram.md) and [`[telegram]`](../config/telegram.md).

```toml
[telegram]
enabled = false                          # Built-in default true. 5.3+: false also stops all Telegram alerts
token = ""
admins = []
channels = []
```

!!! info "PoracleNG 5.3+: `enabled = false` really disables a platform"
    `[discord] enabled = false` or `[telegram] enabled = false` now stops that platform's alerts as well as its commands (Discord webhooks count as Discord). In 5.2.x it only stopped the gateway. `config.example.toml` ships `[telegram] enabled = false`, so if you added a Telegram token and left that line, set it to `true`. Startup warns when a platform has a token but is disabled.

## Geocoding

See [Geocoding](geocoding.md), [Static Maps](staticmaps.md) and [`[geocoding]`](../config/geocoding.md).

```toml
[geocoding]
provider = "none"                        # none, nominatim, photon, google
provider_url = ""                        # Nominatim/Photon URL
geocoding_key = []                       # Google keys
forward_only = false
cache_detail = 3
intersection_users = []                  # GeoNames usernames for {{intersection}}
static_provider = "none"                 # none, tileservercache, rampardos, google, osm, mapbox
static_provider_url = ""
static_internal_url = ""                 # Optional private tileserver URL
static_key = []
```

## Tuning

!!! warning
    Do not change these settings without consulting the Poracle community on Discord.

Full reference: [`[tuning]`](../config/tuning.md).

```toml
[tuning]
reload_interval_secs = 60
worker_pool_size = 4
render_pool_size = 8
render_queue_size = 100
tileserver_concurrency = 2
tileserver_timeout = 10000
tileserver_queue_size = 100
tileserver_deadline = 10000
tileserver_failure_threshold = 5
tileserver_cooldown_ms = 30000
tileserver_pregen_ttl = 86400
delivery_queue_size = 200                # Per destination
concurrent_discord_destinations = 10
concurrent_telegram_destinations = 10
concurrent_discord_webhooks = 10
```

## Alert Limits

Full reference: [`[alert_limits]`](../config/alert_limits.md).

```toml
[alert_limits]
timing_period = 240
dm_limit = 20
channel_limit = 40
dm_summary_limit = 10                    # Separate bucket for summaries
channel_summary_limit = 40
max_limits_before_stop = 10
disable_on_stop = false
shame_channel = ""                       # Discord channel ID

# [[alert_limits.overrides]]
# target = "channel_or_user_id"
# limit = 100
```

## Tracking

Full reference: [`[tracking]`](../config/tracking.md).

```toml
[tracking]
everything_flag_permissions = "allow-any"
default_distance = 0
max_distance = 0
enable_gym_battle = false
default_user_tracking_level_cap = 0
pokemon_change_tracking = true
pokemon_edit = true                      # 5.3+
pokemon_edit_window_mins = 5             # 5.3+
default_edit = []                        # 5.3+
quest_summary_enabled = true
quest_summary_buffer_ttl_hours = 24
```

## Summaries, snapshots, validation, autocreate, AI

```toml
[summariser]
max_per_message = 25

[snapshots]
enabled = false                          # Required for interactive buttons

[validation]
url = ""                                 # Empty = off
fail_mode = "open"

[autocreate]
removal_safety_max_percent = 20          # Built-in default is 0 (off) if omitted

[ai]
enabled = false
suggest_on_dm = false
```

References: [`[summariser]`](../config/summariser.md), [`[snapshots]`](../config/snapshots.md), [`[validation]`](../config/validation.md), [`[autocreate]`](../config/autocreate.md), [`[ai]`](../config/ai.md).

## Reconciliation

See [`[reconciliation.discord]`](../config/reconciliation-discord.md) and [`[reconciliation.telegram]`](../config/reconciliation-telegram.md).

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

Full reference: [`[stats]`](../config/stats.md).

```toml
[stats]
min_sample_size = 10000
window_hours = 8
refresh_interval_mins = 5
rarity_group_2_uncommon = 1.0
rarity_group_3_rare = 0.5
rarity_group_4_very_rare = 0.03
rarity_group_5_ultra_rare = 0.01
```

## Fallbacks

Full reference: [`[fallbacks]`](../config/fallbacks.md).

```toml
[fallbacks]
static_map = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/staticMap.png"
img_url = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/mon.png"
img_url_weather = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/weather.png"
img_url_egg = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/uni.png"
img_url_gym = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/gym.png"
img_url_pokestop = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/pokestop.png"
pokestop_url = "https://raw.githubusercontent.com/jfberry/PoracleNG/images/fallback/pokestop.png"
```

## Logging

Full reference: [`[logging]`](../config/logging.md) and [`[webhookLogging]`](../config/webhookLogging.md).

```toml
[logging]
level = "info"                           # trace, debug, info, warn ("verbose" = info, "silly" = trace)
file_logging_enabled = true
filename = "logs/processor.log"
max_size = 50
max_age = 7
max_backups = 5
compress = true

[webhookLogging]
enabled = false
filename = "logs/webhooks.log"
max_size = 100
max_age = 1
max_backups = 12
compress = true
rotate_interval = 60
```
