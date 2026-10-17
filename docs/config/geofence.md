# `[geofence]`

Defines the geofences (named areas) Poracle uses for area-based tracking.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `paths` | array(string) | `["geofences/geofence.json"]` | Paths or URLs to geofence files. Supports the format created by [geo.jasparke.net](http://geo.jasparke.net/) and standard GeoJSON. |
| `default_name` | string | `"city"` | Name prefix for fences without a name. Unnamed fences become `city1`, `city2`, ... |

## `[geofence.koji]`

Optional integration with [Koji](https://github.com/TurtIeSocks/Koji) for HTTP-served geofences.

| Field | Type | Default | Description |
|---|---|---|---|
| `bearer_token` | string | `""` | Koji API bearer token used for authenticated downloads. |
| `cache_dir` | string | `".cache/geofences"` | Directory for cached copies of downloaded geofences, relative to `config/`. Used as a fallback when Koji is unreachable. |

See also: [Geofence configuration guide](../configuration/geofence.md).
