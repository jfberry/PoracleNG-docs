# `[fallbacks]`

URLs used when image lookups, tile generation, or geocoding fails. These are the images embedded into alerts when the primary source is unavailable.

## Fields

| Field | Type | Description |
|---|---|---|
| `static_map` | string | Fallback static map image. |
| `img_url` | string | Generic monster fallback. |
| `img_url_weather` | string | Weather alert fallback. |
| `img_url_egg` | string | Raid egg fallback. |
| `img_url_gym` | string | Gym fallback. |
| `img_url_pokestop` | string | Pokestop fallback. |
| `pokestop_url` | string | Quest/pokestop fallback (template usage). |

The defaults point at the PoracleJS fallback image repository on GitHub. Override these only if you self-host fallback assets.
