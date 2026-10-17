# Geocoding

Geocoding converts coordinates to street addresses for use in alert templates. Static map providers generate map images embedded in alerts.

## Address Geocoding

### Provider

```toml
[geocoding]
provider = "none"                        # none, nominatim, photon, or google
provider_url = ""                        # URL of your Nominatim or Photon instance
forward_only = false                     # Disable reverse geocoding
cache_detail = 3                         # Decimal places for cache key (3 or 4)
```

### Nominatim (Recommended)

[Nominatim](https://nominatim.org/) is a free, self-hosted geocoding service. Use the [Docker image](https://github.com/mediagis/nominatim-docker) for a local instance:

```toml
[geocoding]
provider = "nominatim"
provider_url = "http://localhost:8080"
```

!!! tip
    Self-hosting Nominatim avoids rate limits and is much faster than public instances.

### Photon

[Photon](https://github.com/komoot/photon) is another self-hostable geocoder:

```toml
[geocoding]
provider = "photon"
provider_url = "https://photon.example.com"
```

### Google

```toml
[geocoding]
provider = "google"
geocoding_key = ["your-google-api-key"]
```

Multiple API keys can be provided — Poracle will cycle through them:

```toml
geocoding_key = ["key1", "key2", "key3"]
```

### Address Format

Customize how addresses are constructed for the `{{addr}}` DTS variable:

```toml
[locale]
address_format = ""                      # default: country-idiomatic formattedAddress
# address_format = "{{{streetName}}} {{streetNumber}}"   # street line only (old default)
address_include_country = false
```

See [`[locale]`](../config/locale.md#address_format) for the available fields and helpers.

### Street Intersections

Fill the `{{intersection}}` DTS field with the nearest street intersection using [GeoNames](https://www.geonames.org). Register one or more free GeoNames usernames:

```toml
[geocoding]
intersection_users = ["geonames-user-1", "geonames-user-2"]
```

Results are cached with the address cache, but each uncached lookup uses a GeoNames credit.

### Failures

The geocoder has a circuit breaker. After repeated failures, lookups are skipped for a cooldown period, so alerts go out without an address instead of waiting. Tune it with `[tuning] geocoding_*` (see [`[tuning]`](../config/tuning.md#geocoding)).

## Static Maps

### Provider

```toml
[geocoding]
static_provider = "none"                 # none, tileservercache, rampardos, google, osm, mapbox
static_provider_url = ""                 # Public provider URL (used in alerts)
static_internal_url = ""                 # Optional private URL for PoracleNG's own tileserver calls
static_key = [""]                        # API keys (google, osm/MapQuest, mapbox)
```

### Map Dimensions

Used by the `google`, `osm` and `mapbox` providers. Tileserver sizes are set in `tileserver_settings` below.

```toml
[geocoding]
width = 320
height = 200
zoom = 15
type = "klokantech-basic"               # Google map type
```

### TileserverCache (Recommended)

[SwiftTileserverCache](https://github.com/123FLO321/SwiftTileserverCache) provides high-quality, customizable map tiles:

```toml
[geocoding]
static_provider = "tileservercache"
static_provider_url = "http://localhost:8081"
```

Rampardos, a newer tileserver written by Unown, uses the same protocol. Templates for it are in `tileserver_templates/rampardos/` in the PoracleNG repository:

```toml
[geocoding]
static_provider = "rampardos"
static_provider_url = "http://your-rampardos-host:port"
```

If the public URL in `static_provider_url` goes through a proxy such as Cloudflare, set `static_internal_url` to a direct internal address. PoracleNG then uses it for its own requests, while alerts still contain the public URL.

#### Time-of-Day Styles

Use different map styles based on time of day:

```toml
[geocoding]
day_style = "osm-bright"
dawn_style = "osm-liberty"
dusk_style = "osm-liberty"
night_style = "dark-matter"
```

Reference these in DTS templates with `#(style)`.

#### Per-Type Settings

Override map settings for specific alert types:

```toml
[geocoding.tileserver_settings.default]
type = "staticMap"                       # staticMap or multiStaticMap
width = 500
height = 250
zoom = 15
pregenerate = true
include_stops = false

[geocoding.tileserver_settings.monster]
type = "staticMap"
include_stops = true

[geocoding.tileserver_settings.raid]
width = 600
height = 300
```

Map types include `monster`, `raid`, `egg`, `quest`, `lure`, `invasion`, `gym`, `nest`, `maxbattle`, `showcase`, `weatherchange`, `location` and `questsummary`. Per-type entries can also set `ttl` (seconds). See [`[geocoding]`](../config/geocoding.md#geocodingtileserver_settingstype) for the full list and `[geocoding.static_map_type]`.

### Google Maps

```toml
[geocoding]
static_provider = "google"
static_key = ["your-google-api-key"]
```

### OpenStreetMap (MapQuest)

The `osm` provider renders through the MapQuest static map API and needs a MapQuest key:

```toml
[geocoding]
static_provider = "osm"
static_key = ["your-mapquest-key"]
```

### Mapbox

```toml
[geocoding]
static_provider = "mapbox"
static_key = ["your-mapbox-token"]
```
