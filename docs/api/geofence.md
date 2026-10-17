# Geofence API

Read the loaded geofences, trigger a reload, and generate static map images of areas and locations. All endpoints are under `/api/geofence/` and need `X-Poracle-Secret` (see [API overview](index.md#authentication)).

## Geofence data

### `GET /api/geofence/all`

Every loaded geofence (from files and Koji) with its display metadata and polygon (`path`, or `multipath` for multi-polygon fences).

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/all
```

```json
{
  "status": "ok",
  "geofence": [
    {"name": "Canterbury", "id": 1, "color": "#3388ff", "group": "Kent", "description": "", "userSelectable": true, "displayInMatches": true,
     "path": [[51.29, 1.05], [51.30, 1.10], [51.26, 1.11]]}
  ]
}
```

### `GET /api/geofence/all/geojson`

The same fences as a GeoJSON `FeatureCollection`, wrapped in `{status, geoJSON}`. Each feature's `properties` carry `name`, `color`, `id`, `group`, `description`, `userSelectable` and `displayInMatches`.

```json
{"status": "ok", "geoJSON": {"type": "FeatureCollection", "features": [{"type": "Feature", "properties": {"name": "Canterbury", "...": "..."}, "geometry": {"type": "Polygon", "coordinates": [[[1.05, 51.29], "..."]]}}]}}
```

### `GET /api/geofence/all/hash`

An MD5 hash **per area**, so a client can tell which fences changed without downloading polygons:

```json
{"status": "ok", "areas": {"Canterbury": "6f1ed002ab5595859014ebf0951522d9", "Dover": "0cc175b9c0f1b6a831c399e269772661"}}
```

### `GET` or `POST /api/geofence/reload`

A **full** reload: re-reads geofence files from disk, re-fetches Koji fences, rebuilds the spatial index, then reloads humans and tracking rules from the database. Returns `{"status": "ok"}`. For a database-only reload use `/api/reload` (see [Reload endpoints](index.md#reload-endpoints)).

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/reload
```

## Static map tiles

These generate an image through your configured [static map provider](../configuration/staticmaps.md) and return its **URL as JSON** — they don't return image bytes:

```json
{"status": "ok", "url": "http://tileserver:9000/staticmap/pregenerated/abc123"}
```

| Method | Path | Image |
|---|---|---|
| `GET` | `/api/geofence/{area}/map` | One area's polygon (`404` for an unknown area or one with no polygon) |
| `POST` | `/api/geofence/overviewMap` | Several areas in different colours; body `{"areas": ["canterbury", "dover"]}` (`404` if none match) |
| `GET` | `/api/geofence/locationMap/{lat}/{lon}` | A pin at the location |
| `GET` | `/api/geofence/distanceMap/{lat}/{lon}/{distance}` | A circle of `distance` metres around the point |
| `GET` | `/api/geofence/weatherMap/{lat}/{lon}` | The weather S2 cell covering the point; optional `?weather=` condition id to override current weather |

All return `503` if no static map provider is configured, and `500` if tile generation fails (check the processor log for `staticmap:` warnings).

```sh
URL=$(curl -s -H "X-Poracle-Secret: your-secret" \
  http://localhost:3030/api/geofence/downtown/map | jq -r .url)
curl -o downtown.png "$URL"
```
