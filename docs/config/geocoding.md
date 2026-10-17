# `[geocoding]`

Address lookups, nearest-intersection lookups, static map tiles, and time-of-day style overrides.

## Fields

### Address lookups

| Field | Type | Default | Description |
|---|---|---|---|
| `provider` | string | `"none"` | Address lookup provider: `none`, `nominatim` (recommended), `photon`, or `google`. |
| `provider_url` | string | `""` | Base URL of your Nominatim or Photon instance. |
| `geocoding_key` | array(string) | `[]` | Google Geocoding API keys. Poracle rotates through them. |
| `forward_only` | bool | `false` | Disable reverse geocoding (coordinates to address). Forward lookups (place name to coordinates, e.g. `!location`) still work. |
| `cache_detail` | int | `3` | Decimal places of lat/lon used as the cache key. `4` gives a 100x finer cache. |
| `intersection_users` | array(string) | `[]` | [GeoNames](https://www.geonames.org) usernames used to fill the `{{intersection}}` DTS field with the nearest street intersection. One is picked at random for each uncached lookup to spread credit use. Empty = off. Requires a restart. |

Address results are cached on disk in `config/.cache/geocache`. Intersection results share the same cache, but each uncached lookup uses a GeoNames credit.

### Static maps

| Field | Type | Default | Description |
|---|---|---|---|
| `static_provider` | string | `"none"` | Map tile provider: `none`, `tileservercache` ([SwiftTileserverCache](https://github.com/123FLO321/SwiftTileserverCache)), `rampardos`, `google`, `osm`, or `mapbox`. `rampardos` uses the same protocol as `tileservercache`. `osm` renders through the MapQuest static map API and needs a MapQuest key in `static_key`. |
| `static_provider_url` | string | `""` | URL of the tile provider. This is the URL that ends up in alerts, so Discord/Telegram must be able to reach it. |
| `static_internal_url` | string | `""` | Optional private URL PoracleNG uses for its **own** tileserver calls (render, pregenerate, image upload pre-fetch). Use when `static_provider_url` is a public HTTPS address (e.g. behind Cloudflare) but the tileserver is directly reachable on your internal network. Empty = use `static_provider_url`. |
| `static_key` | array(string) | `[]` | Static map API keys for `google`, `osm` (MapQuest) or `mapbox`. Poracle rotates. |
| `width` | int | `320` | Default map width in pixels (non-tileserver providers). |
| `height` | int | `200` | Default map height in pixels (non-tileserver providers). |
| `zoom` | int | `15` | Default zoom level (non-tileserver providers). |
| `type` | string | `"klokantech-basic"` | Map type passed to the Google static map API (`google` provider only). |
| `day_style` | string | `""` | Tile style used during the day. |
| `dawn_style` | string | `""` | Tile style used at dawn. |
| `dusk_style` | string | `""` | Tile style used at dusk. |
| `night_style` | string | `""` | Tile style used at night. |

Use `style: "#(style)"` inside tileserver templates to switch styles by time of day.

!!! note "Unused keys"
    `sprite_height`, `sprite_width` and `scale` are not read by PoracleNG and can be removed. The 5.3 `config.example.toml` no longer includes them.

### Circuit breakers

The geocoder, the GeoNames intersection lookup, the tileserver and the Shlink shortener each have a circuit breaker. After several failures in a row the service is skipped for a cooldown period, so alerts are sent without it instead of waiting for timeouts. Geocoder and intersection limits come from `[tuning] geocoding_*`, and tileserver limits from `[tuning] tileserver_*`. Shlink always uses the defaults (5 failures, 30 s). See [`[tuning]`](tuning.md).

## `[geocoding.tileserver_settings.<type>]`

Tileserver request settings. `default` applies to every map type without its own entry.

| Field | Type | Default | Description |
|---|---|---|---|
| `type` | string | `"staticMap"` | `staticMap` or `multiStaticMap`. |
| `width` | int | `500` | Tile width. |
| `height` | int | `250` | Tile height. |
| `zoom` | int | `15` | Zoom level. |
| `pregenerate` | bool | `true` | Pregenerate the tile on the tileserver before sending its URL. |
| `include_stops` | bool | `false` | Overlay nearby pokestops and gyms on the tile (needs `multiStaticMap` and a [scanner database](database.md#databasescanner)). |
| `ttl` | int | `0` | Tile cache TTL in seconds. `0` = use [`[tuning] tileserver_pregen_ttl`](tuning.md#tileserver). |

On per-type entries, leave a field out to inherit it from `default`.

Map types include: `monster`, `raid`, `egg`, `quest`, `lure`, `invasion`, `gym`, `nest`, `maxbattle`, `showcase`, `fort-update`, `weatherchange`, `location`, `distance`, `area`, `areaoverview`, `weather`, `questsummary`.

```toml
[geocoding.tileserver_settings.default]
type = "staticMap"
width = 500
height = 250
zoom = 15
pregenerate = true
include_stops = false

[geocoding.tileserver_settings.monster]
type = "multiStaticMap"
include_stops = true
```

## `[geocoding.static_map_type]`

Chooses the tileserver template variant per map type. Every map type defaults to `staticMap` (one tile from `/staticmap/poracle-<type>`). Set `multiStaticMap` only if your tileserver has a matching `poracle-multi-<type>.json` template. Prefix the value with `*` (e.g. `"*staticMap"`) to turn off pregeneration for that type.

Keys are map type names as above, except that pokemon alerts use the key `pokemon` (not `monster`).

```toml
[geocoding.static_map_type]
questsummary = "staticMap"
```

See also: [Geocoding configuration](../configuration/geocoding.md), [Static maps](../configuration/staticmaps.md).
