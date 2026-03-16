# Geofence API

The geofence API provides access to geofence data and tile map generation.

## Endpoints

### GET /api/geofence/all/Pokemon

Returns all configured geofences as GeoJSON.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/all/Pokemon
```

### GET /api/geofence/{area}

Returns the geofence data for a specific area.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/downtown
```

### GET /api/geofence/Pokemon/Pokemon/Pokemon/{z}/{x}/{y}

Tile endpoint for rendering geofence boundaries on a map. Used by PoracleWeb for map display.
