# Geofence API

The geofence API exposes loaded geofence data and on-demand static map tile generation. All endpoints below sit under `/api/geofence/` and require the `X-Poracle-Secret` header (see [API authentication](index.md#authentication)).

## Data Endpoints

### `GET /api/geofence/all`

Returns the raw geofence data currently loaded by the processor.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/all
```

### `GET /api/geofence/all/geojson`

Returns all loaded geofences as a single GeoJSON `FeatureCollection`.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/all/geojson
```

### `GET /api/geofence/all/hash`

Returns a hash of the currently loaded geofence set. Useful for clients (e.g. PoracleWeb) that want to detect when the server's geofences have changed without re-downloading the full payload.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/all/hash
```

### `POST /api/geofence/reload`

Triggers an in-place reload of geofences from disk. Also exposed as `GET` for convenience.

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/reload
```

## Static Map Endpoints

These endpoints render static map tiles via the configured tileserver. They are used by PoracleWeb for previews and by templates that embed maps.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/geofence/:area/map` | Render a map of a single named area |
| `GET` | `/api/geofence/locationMap/:lat/:lon` | Render a map centred on a point |
| `GET` | `/api/geofence/distanceMap/:lat/:lon/:distance` | Render a point with a distance ring |
| `GET` | `/api/geofence/weatherMap/:lat/:lon` | Render a map with weather cell overlay |
| `POST` | `/api/geofence/overviewMap` | Render an overview map (POST body specifies the request) |

```sh
# Example: render a map of the "downtown" geofence
curl -H "X-Poracle-Secret: your-secret" \
  http://localhost:3030/api/geofence/downtown/map -o downtown.png
```
