# `[geocoding]`

Address lookups, map tile rendering, and time-of-day style overrides.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `provider` | string | `"none"` | Address lookup provider: `none`, `nominatim` (recommended), or `google`. |
| `provider_url` | string | `""` | Endpoint for self-hosted Nominatim. |
| `forward_only` | bool | `false` | Disable reverse geocoding lookups when `true`. |
| `cache_detail` | int | `3` | Decimal places of lon/lat used as cache key. Higher = more granular cache. |
| `day_style` | string | `""` | Tile style used during the day. |
| `dawn_style` | string | `""` | Tile style used at dawn. |
| `dusk_style` | string | `""` | Tile style used at dusk. |
| `night_style` | string | `""` | Tile style used at night. |
| `static_provider` | string | `"none"` | Map tile provider: `tileservercache`, `google`, `osm`, or `mapbox`. |
| `static_provider_url` | string | `""` | URL of the tile provider. |
| `geocoding_key` | array(string) | `[""]` | Geocoding API keys (Google). Poracle rotates through them. |
| `static_key` | array(string) | `[""]` | Static map API keys (Google or Mapquest). Poracle rotates. |
| `width` | int | `320` | Default map width in pixels. |
| `height` | int | `200` | Default map height in pixels. |
| `zoom` | int | `15` | Default zoom level. |
| `sprite_height` | int | `20` | Height of sprite icons placed on the map. |
| `sprite_width` | int | `20` | Width of sprite icons placed on the map. |
| `scale` | int | `2` | Map scale factor (HiDPI). |
| `type` | string | `"klokantech-basic"` | Map style type. |

Use `style: "#(day)"` (or `dawn`/`dusk`/`night`) inside templates to switch styles by time of day.

## `[geocoding.tileserver_settings.default]`

Default tileserver request used unless overridden per alert type.

| Field | Type | Default | Description |
|---|---|---|---|
| `type` | string | `"staticMap"` | `staticMap` or `multiStaticMap`. |
| `width` | int | `500` | Tile width. |
| `height` | int | `250` | Tile height. |
| `zoom` | int | `15` | Zoom level. |
| `pregenerate` | bool | `true` | Pregenerate tiles where supported. |
| `include_stops` | bool | `false` | Include nearby pokestops on the tile. |

Override per alert type (`monster`, `raid`, `pokestop`, `quest`, `weather`, `location`, `nest`, `gym`):

```toml
[geocoding.tileserver_settings.monster]
type = "staticMap"
include_stops = true
```

See also: [Geocoding configuration](../configuration/geocoding.md), [Static maps](../configuration/staticmaps.md).
