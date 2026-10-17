# Advanced Configuration

Advanced settings and features for experienced administrators.

## Shortlink Providers

Shorten URLs marked with `<S< >S>` in DTS templates. Only [Shlink](https://shlink.io/) is implemented; any other value leaves links unshortened.

```toml
[general]
shortlink_provider = "shlink"
shortlink_provider_url = "https://shlink.example.com"
shortlink_provider_key = "your-shlink-api-key"
shortlink_provider_domain = ""           # Optional domain override
```

If Shlink fails 5 times in a row, shortening is skipped for 30 seconds and alerts are sent with the full URLs.

## Map Links

Add links to mapping tools in your DTS templates:

```toml
[general]
react_map_url = "https://myReactMap.com/"
diadem_url = "https://myDiadem.com/"
rdm_url = "https://myRDM.com/"
rocket_mad_url = "https://myRocketMAD.com/"
```

These populate the `{{reactMapUrl}}`, `{{diademUrl}}`, `{{rdmUrl}}` and `{{rocketMadUrl}}` DTS variables.

## Image URLs

Configure icon repositories:

```toml
[general]
img_url = "https://raw.githubusercontent.com/nileplumb/PkmnShuffleMap/master/UICONS"
img_url_alt = ""                         # Alternative icon repository
sticker_url = "https://raw.githubusercontent.com/bbdoc/tgUICONS/main/Shuffle"
request_shiny_images = false             # Request shiny-variant images
```

!!! note
    The per-alert-type `[general.images]` / `[general.stickers]` tables from PoracleJS are not supported and are ignored.

## IP Restrictions

Restrict which client IPs can reach PoracleNG:

```toml
[processor]
ip_whitelist = ["192.168.1.0/24", "203.0.113.7"]
ip_blacklist = ["10.0.0.5"]
trusted_proxies = []                     # 5.3+: list your reverse proxy here if you have one
```

- The lists apply to every route, including the webhook receiver, so a whitelist must include your scanner's IP.
- **5.3+:** entries can be exact IPs or CIDR ranges (IPv4 or IPv6), and an invalid entry stops startup. In 5.2.x only exact IPs work, and invalid entries are silently ignored.
- **5.3+:** `X-Forwarded-For` / `X-Real-IP` are only believed from addresses in `trusted_proxies`; otherwise the client IP is the TCP peer. **If PoracleNG is behind a reverse proxy (nginx, Traefik, a Docker network), add the proxy to `trusted_proxies`**, or every request appears to come from the proxy. See [`[processor]`](../config/processor.md).
- In 5.2.x the client IP honours `X-Forwarded-For` from anyone, so a client can spoof it. On 5.2.x, treat the IP lists as a convenience, not as access control; use `api_secret` and a firewall.

PoracleNG is a single binary, and `[processor]` is its only listener. IP lists under an old `[alerter]` section are not read.

## Tuning

!!! warning
    Only change tuning settings after consulting the Poracle community on Discord.

```toml
[tuning]
reload_interval_secs = 60                # Reload tracking rules from the database
worker_pool_size = 4                     # Webhooks processed concurrently
render_pool_size = 8                     # Concurrent DTS renders
delivery_queue_size = 200                # Buffer per destination
concurrent_discord_destinations = 10     # Concurrent Discord DM/channel sends
concurrent_telegram_destinations = 10    # Concurrent Telegram sends
concurrent_discord_webhooks = 10         # Concurrent Discord webhook sends
```

See [`[tuning]`](../config/tuning.md) for the tileserver, geocoding and validation settings, and for keys that no longer have any effect.

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
