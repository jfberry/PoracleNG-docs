# Config API

The config API provides read-only access to configuration data, primarily used by PoracleWeb.

## Endpoints

### GET /api/config/poracleWeb

Returns the PoracleWeb-relevant configuration.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/config/poracleWeb
```

### GET /api/masterdata/pokemon

Returns pokemon game data (names, types, forms).

```sh
curl http://localhost:3030/api/masterdata/pokemon
```

### GET /api/masterdata/items

Returns item data.

### GET /api/masterdata/moves

Returns move data.

### GET /api/masterdata/grunts

Returns Team Rocket grunt data.

### GET /api/masterdata/quests

Returns quest data.
