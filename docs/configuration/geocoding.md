# Geocoding

Geocoding converts coordinates to street addresses for use in alert templates. Static map providers generate map images embedded in alerts.

## Address Geocoding

### Provider

```toml
[geocoding]
provider = "none"                        # none, nominatim, or google
provider_url = ""                        # URL for nominatim or google
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
address_format = "{{{streetName}}} {{streetNumber}}"
```

## Static Maps

### Provider

```toml
[geocoding]
static_provider = "none"                 # none, tileservercache, google, osm, mapbox
static_provider_url = ""                 # Provider URL
static_key = [""]                        # API keys (for google/mapbox)
```

### Map Dimensions

```toml
[geocoding]
width = 320
height = 200
zoom = 15
sprite_height = 20
sprite_width = 20
scale = 2
type = "klokantech-basic"               # Map style
```

### TileserverCache (Recommended)

[SwiftTileserverCache](https://github.com/123FLO321/SwiftTileserverCache) provides high-quality, customizable map tiles:

```toml
[geocoding]
static_provider = "tileservercache"
static_provider_url = "http://localhost:8081"
```

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

Valid alert types: `monster`, `raid`, `pokestop`, `quest`, `weather`, `location`, `nest`, `gym`

### Google Maps

```toml
[geocoding]
static_provider = "google"
static_key = ["your-google-api-key"]
```

### OpenStreetMap

```toml
[geocoding]
static_provider = "osm"
```

### Mapbox

```toml
[geocoding]
static_provider = "mapbox"
static_key = ["your-mapbox-token"]
```
