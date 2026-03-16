# Advanced Configuration

Advanced settings and features for experienced administrators.

## Shortlink Providers

Shorten URLs in alert messages:

```toml
[general]
shortlink_provider = "hideuri"           # hideuri, shlink, or yourls
shortlink_provider_url = ""              # Provider URL (for shlink/yourls)
shortlink_provider_key = ""              # API key
shortlink_provider_domain = ""           # Custom domain (if supported)
```

## Map Links

Add links to mapping tools in your DTS templates:

```toml
[general]
rdm_url = "https://myRDM.com/"
react_map_url = "https://myReactMap.com/"
rocket_mad_url = "https://myRocketMAD.com/"
```

These populate the `{{rdmUrl}}`, `{{reactMapUrl}}`, and `{{rocketMadUrl}}` DTS variables.

## Image URLs

Configure icon repositories:

```toml
[general]
img_url = "https://raw.githubusercontent.com/nileplumb/PkmnShuffleMap/master/UICONS"
img_url_alt = ""                         # Alternative icon repository
sticker_url = "https://raw.githubusercontent.com/bbdoc/tgUICONS/main/Shuffle"
request_shiny_images = false             # Request shiny-variant images
```

Override per alert type:

```toml
[general.images]
monster = "https://custom-repo.com/icons"

[general.stickers]
monster = "https://custom-repo.com/stickers"
```

Valid types: `monster`, `gym`, `nest`, `invasion`, `lure`, `quest`, `raid`, `weather`

## IP Restrictions

Restrict which IPs can access the processor and alerter:

```toml
[processor]
ip_whitelist = ["192.168.1.0/24"]

[alerter]
ip_whitelist = ["192.168.1.0/24"]
ip_blacklist = ["10.0.0.5"]
```

## Tuning

!!! warning
    Only change tuning settings after consulting the Poracle community on Discord.

### Processor Tuning

```toml
[tuning]
reload_interval_secs = 60               # DB reload interval for tracking changes
encounter_cache_ttl = 3600               # Seconds to cache encounter data
worker_pool_size = 4                     # Matching worker goroutines
batch_size = 50                          # Results per batch to alerter
flush_interval_millis = 100              # Max time before flushing partial batch
```

### Alerter Tuning

```toml
[tuning]
max_database_connections = 15            # Max DB connections per worker
concurrent_matched_processors = 10       # Concurrent matched payload processors
matched_max_queue_size = 5000            # Max buffered items before backpressure
concurrent_discord_destinations = 10     # Concurrent Discord sends per bot
concurrent_telegram_destinations = 10    # Concurrent Telegram sends per bot
concurrent_discord_webhooks = 10         # Concurrent Discord webhook sends
```

## Rarity Stats

Configure how pokemon rarity is calculated:

```toml
[stats]
min_sample_size = 10000                  # Min sightings before calculating
window_hours = 8                         # Rolling window for calculations
refresh_interval_mins = 5                # Recalculation frequency
rarity_group_2_uncommon = 1.0            # % threshold
rarity_group_3_rare = 0.5
rarity_group_4_very_rare = 0.03
rarity_group_5_ultra_rare = 0.01
```

Rarity is calculated by the processor based on observed pokemon sightings. Groups are:

1. Common (default)
2. Uncommon (< 1% of total sightings)
3. Rare (< 0.5%)
4. Very Rare (< 0.03%)
5. Ultra Rare (< 0.01%)

## Fallback Images

URLs used when normal image sources fail:

```toml
[fallbacks]
static_map = "..."
img_url = "..."
img_url_weather = "..."
img_url_egg = "..."
img_url_gym = "..."
img_url_pokestop = "..."
pokestop_url = "..."
```
