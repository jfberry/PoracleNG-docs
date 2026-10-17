# Config & game data API

Endpoints for reading server configuration, editing `config.toml` from a UI, and looking up game data, weather, statistics and geocoding. All require `X-Poracle-Secret` (see [API overview](index.md#authentication)). Errors are `application/problem+json`.

## Server settings for web UIs

### `GET /api/config/poracleWeb`

The configuration subset a user-facing web UI (PoracleWeb, ReactMap) needs to render forms correctly.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/config/poracleWeb
```

```json
{
  "status": "ok",
  "version": "5.3.0",
  "locale": "en",
  "prefix": "!",
  "availableLanguages": ["de", "en"],
  "admins": {"discord": ["344179542874914817"], "telegram": []},
  "defaultDistance": 0,
  "maxDistance": 5000,
  "defaultEdit": ["raid"],
  "defaultTemplateName": "1",
  "disabledHooks": ["nest"],
  "everythingFlagPermissions": "allow-any",
  "gymBattles": true,
  "provider": "nominatim",
  "providerURL": "https://nominatim.example.com",
  "addressFormat": "{{{streetName}}} {{streetNumber}}",
  "staticKey": [],
  "pvpCaps": [50],
  "defaultPvpCap": 50,
  "pvpFilterMaxRank": 100,
  "pvpFilterGreatMinCP": 1400,
  "pvpFilterUltraMinCP": 2350,
  "pvpFilterLittleMinCP": 450,
  "pvpLittleLeagueAllowed": true,
  "pvpRequiresMinCp": false,
  "channelNotesContainsCategory": false
}
```

Fields clients most often need:

| Field | Meaning |
|---|---|
| `availableLanguages` | Languages the set-language endpoints accept, sorted. **`null` means unrestricted** — offer your full list. |
| `maxDistance` | `[tracking] max_distance` in metres (`0` = no limit). v2 tracking clamps `distance` to it (5.3+), so warn users before they save. |
| `defaultDistance` | Distance to pre-fill for new rules. |
| `defaultEdit` | (5.3+) Alert types listed in `[tracking] default_edit`, whose rules edit alerts in place even without the `edit` flag. Always an array. |
| `disabledHooks` | Alert types switched off on this server: any of `pokemon`, `raid`, `fort`, `invasion`, `lure`, `quest`, `weather`, `nest`, `gym`, `maxbattle`, and (5.3+) `showcase`. Hide those tracking forms. |
| `provider` | (5.3+) Geocoder type: `nominatim`, `photon` or `google`; empty when none is configured. Don't infer it from `providerURL`, which Google doesn't use. |
| `admins` | Admin ids from `[discord] admins` / `[telegram] admins`. Always arrays. |
| `pvp*`, `defaultPvpCap` | PVP filter limits and level caps. |

## Config editor

These endpoints power the config editor in the [Poracle Config UI](../configuration/config-ui.md). Only fields in the editor schema are exposed; database credentials, tokens and bind addresses stay TOML-only.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/config/schema` | Field metadata, grouped into `sections` |
| `GET` | `/api/config/values` | Current values (`?section=` for one section) |
| `POST` | `/api/config/values` | Save changes — **rewrites `config/config.toml`** |
| `POST` | `/api/config/validate` | Dry-run validation of the same body |

### `GET /api/config/schema`

Returns `{"status": "ok", "sections": [...]}`. Each field has `name`, `type` (`string`, `int`, `float`, `bool`, `string[]`, `int[]`, `color[]`, `select`, `map`), `default`, `description`, `hotReload`, and optional `sensitive`, `deprecated`, `advanced`, `hideDefault`, `minLength`/`maxLength`, `options` (for `select`), `dependsOn` and `resolve` (an id-resolution hint such as `discord:channel` or `destination`, for use with [`POST /api/resolve`](messaging.md#resolve-ids-to-names)). Sections may also contain `tables` for array-of-tables settings such as communities and delegated admins.

### `GET /api/config/values`

```sh
curl -H "X-Poracle-Secret: your-secret" "http://localhost:3030/api/config/values?section=discord"
```

```json
{"status": "ok", "values": {"discord": {"admins": ["344179542874914817"], "check_role": true}}, "overridden": []}
```

Sensitive fields are returned as `"****"`. `overridden` is always empty; it remains only so older editors don't break.

### `POST /api/config/values`

Send only the fields you're changing, nested by section:

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"discord": {"admins": ["344179542874914817", "999888777"]}, "alert_limits": {"dm_limit": 30}}' \
  http://localhost:3030/api/config/values
```

```json
{"status": "ok", "saved": 2, "restart_required": true, "restart_fields": ["discord.admins"], "backup": "backups/config.toml.bak.2026-10-17_101500"}
```

- The change is written **into `config/config.toml`**; the previous file is copied to `config/backups/` first (`backup` is relative to `config/`). Settings outside the editor schema (`[database]`, tokens, `[processor]` host/port) are kept, but the file is re-encoded, so hand-written comments and layout may be lost — recover them from the backup if needed. A value equal to the schema default is removed from the file rather than written out.
- Hot-reloadable settings take effect immediately and trigger a state reload. If `restart_required` is true, restart the processor for `restart_fields` to apply.
- Sending `"****"` for a sensitive field leaves the stored secret unchanged, so an editor can post a whole form back safely.
- Unknown sections/fields, or values that fail validation with severity `error`, are rejected with `400` and nothing is written.

!!! note "No more `overrides.json`"
    Older releases saved editor changes to `config/overrides.json`. That file is now merged into `config.toml` automatically at startup and deleted. There's no `/api/config/migrate` endpoint, despite older documentation describing one.

### `POST /api/config/validate`

Same body as a save; nothing is written.

```json
{
  "status": "ok",
  "issues": [
    {"field": "discord.iv_colors[2]", "severity": "error", "message": "not a valid hex colour (expected #RGB or #RRGGBB): \"red\""},
    {"field": "geofence.paths[1]", "severity": "warning", "message": "file does not exist (yet) at /path/to/config/geofences/foo.json"}
  ]
}
```

`error` issues would block a save; `warning` issues wouldn't. Current checks: colour arrays must be hex colours, array lengths must respect `minLength`/`maxLength`, and `geofence.paths` entries must be URLs or relative paths inside the config directory (missing files are a warning).

## Game data

All three accept `?locale=` (default `en`) to choose the language of names.

| Method | Path | Returns |
|---|---|---|
| `GET` | `/api/masterdata/monsters` | Every pokemon/form keyed by `"<id>_<form>"`: `name`, `id`, `form {id, name}`, `types [{id, name}]`, `stats`, `evolutions` |
| `GET` | `/api/masterdata/grunts` | Grunts keyed by grunt id: `type`, `grunt`, `gender`, rewards and encounters; plus (5.3+) `grunt_type`, `name`, `short_name`; `locale` support is 5.3+ |
| `GET` | `/api/masterdata/costumes` | (5.3+) Costumes keyed by id: `{id, name}` |

```sh
curl -H "X-Poracle-Secret: your-secret" "http://localhost:3030/api/masterdata/monsters?locale=de"
```

```json
{
  "1_0": {"name": "Bisasam", "id": 1, "form": {"name": "", "id": 0}, "types": [{"id": 12, "name": "Pflanze"}, {"id": 4, "name": "Gift"}],
          "stats": {"baseAttack": 118, "baseDefense": 111, "baseStamina": 128}, "evolutions": [{"evoId": 2, "id": 0, "candyCost": 25}]}
}
```

When building invasion pickers, key on `grunt_type` rather than the map key — several grunt ids share one `grunt_type`, and `grunt_type` is what a v2 invasion rule returns.

There are no `/api/masterdata/pokemon`, `items`, `moves` or `quests` endpoints.

### `GET /api/v2/activity` (5.3+)

What the processor has actually seen recently, so pickers can offer what's live rather than the whole game master.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/activity
```

```json
{
  "window_secs": 21600,
  "raid_bosses": [150, 382],
  "max_battle_bosses": [1, 4],
  "quest_pokemon": [327],
  "quest_items": [1, 701],
  "quest_candy": [133],
  "quest_mega": [6],
  "quest_xl": [],
  "invasion_grunts": [4, 5, 44],
  "costumes": [{"pokemon_id": 25, "ids": [1, 5]}],
  "forms": [{"pokemon_id": 19, "ids": [45, 46]}],
  "raid_costumes": [],
  "raid_forms": [{"pokemon_id": 382, "ids": [173]}]
}
```

The window is six hours. Data is in memory and starts empty after a restart; an empty list means nothing was seen, not that a feature is unsupported. Resolve ids with the masterdata endpoints above.

## Weather

### `GET /api/weather?cell={s2CellId}`

```sh
curl -H "X-Poracle-Secret: your-secret" "http://localhost:3030/api/weather?cell=5185794497592262656"
```

Returns an object mapping S2 cell ids (as string keys) to in-game weather condition ids around the given cell:

```json
{"5185794497592262656": 3, "5185794506182197248": 3}
```

A missing `cell` is a `422`.

## Statistics

| Path | Returns |
|---|---|
| `GET /api/stats/rarity` | `{"groups": [{"group": 1, "pokemon_ids": [...]}, ...]}` — current rarity groups (1 = common … 5 = ultra-rare) from the rolling window |
| `GET /api/stats/shiny` | `{"pokemon": [{"pokemon_id": 129, "total": 5120, "seen": 10, "ratio": 512}, ...]}` |
| `GET /api/stats/shiny-possible` | `{"map": {"129": true, ...}}` — species seen shiny in the window |

See [stats configuration](../config/stats.md) for how the window is built.

## Geocoding

Both return `503` when no geocoder is configured. See [Geocoding](../configuration/geocoding.md).

### `GET /api/geocode/forward?q={query}`

```sh
curl -H "X-Poracle-Secret: your-secret" "http://localhost:3030/api/geocode/forward?q=Canterbury"
```

Returns an array of candidates (latitude/longitude plus address detail); empty when nothing matched.

### `GET /api/geocode/reverse?lat=&lon=&language=` (5.3+)

Resolves coordinates to an address, in `language` when given (cached per language). Returns the address object (`formattedAddress`, `streetName`, `streetNumber`, `city`, `zipcode`, `country`, `flag`, …). `404` when nothing resolves — including when `[geocoding] forward_only` disables reverse lookups.
