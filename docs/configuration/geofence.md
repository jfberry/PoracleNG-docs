# Geofences

Geofences define geographic areas that users can subscribe to for alerts. PoracleNG supports three geofence formats and can load from multiple sources.

## Configuration

```toml
[geofence]
paths = ["geofences/geofence.json"]     # Array of file paths to load
default_name = "city"                    # Default name if fence has no name
default_color = "#3399ff"               # Default color for map display
```

Multiple files can be specified — all fences from all files are merged:

```toml
[geofence]
paths = ["geofences/areas.json", "geofences/parks.json", "geofences/custom.geojson"]
```

## Poracle JSON Format

The traditional Poracle format, a JSON array of fences created at [geo.jasparke.net](http://geo.jasparke.net/):

```json
[
  {
    "name": "downtown",
    "color": "#FF0000",
    "path": [
      [40.7128, -74.0060],
      [40.7138, -74.0050],
      [40.7148, -74.0070],
      [40.7128, -74.0060]
    ]
  },
  {
    "name": "uptown",
    "color": "#00FF00",
    "path": [
      [40.7828, -73.9560],
      [40.7838, -73.9550],
      [40.7848, -73.9570],
      [40.7828, -73.9560]
    ]
  }
]
```

Each entry has a `name`, optional `color`, and a `path` array of `[latitude, longitude]` coordinates forming a closed polygon.

## GeoJSON Format

Standard GeoJSON FeatureCollection with Polygon or MultiPolygon geometries:

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {
        "name": "downtown"
      },
      "geometry": {
        "type": "Polygon",
        "coordinates": [[
          [-74.0060, 40.7128],
          [-74.0050, 40.7138],
          [-74.0070, 40.7148],
          [-74.0060, 40.7128]
        ]]
      }
    }
  ]
}
```

!!! note
    GeoJSON uses `[longitude, latitude]` order, which is the opposite of Poracle format's `[latitude, longitude]`.

The fence name is taken from `properties.name` in each feature.

## Koji Integration

[Koji](https://github.com/TurtIeSern/Koji) is a geofence management tool. PoracleNG can download geofences directly from a Koji instance:

```toml
[geofence.koji]
bearer_token = "your-koji-api-token"
cache_dir = ".cache/geofences"
```

When a Koji token is configured, PoracleNG downloads geofences from Koji and caches them locally. The cache is shared between the processor and alerter.

To use Koji fences, add the Koji project URL to the geofence paths:

```toml
[geofence]
paths = ["koji://project-name", "geofences/local-extra.json"]
```

You can mix Koji sources with local files.

## Area Names in Commands

Users reference geofence names in commands like `!area add downtown`. The name matching is case-insensitive.

Admins and users can see available areas with `!area list`.
