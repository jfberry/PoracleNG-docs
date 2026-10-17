# Legacy v1 API (tracking, humans, profiles)

!!! warning "Deprecated — use v2 for new work"
    These endpoints are **frozen**: they still work and have no removal date, but they get no new features (no `incident` type, no saved-location update, no mutes, no user listing/deletion). New clients should use [`/api/v2`](v2/index.md); existing clients can follow [Migrating from v1](v2/migration.md).

v1 is lenient: it accepts a single object or an array, coerces `"1"`/`true` where it can, and returns `{"status":"ok", …}` on success or `{"status":"error","message":"…"}` on failure. All calls need `X-Poracle-Secret`. `{id}` is the human's Discord/Telegram id or webhook name.

## Tracking

Every type follows the same four endpoints. Types: `pokemon`, `raid`, `egg`, `quest`, `invasion`, `lure`, `nest`, `gym`, `fort`, `maxbattle`.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/tracking/{type}/{id}` | List rules (each with a `description`) |
| `POST` | `/api/tracking/{type}/{id}` | Create/update rules |
| `DELETE` | `/api/tracking/{type}/{id}/byUid/{uid}` | Delete one rule |
| `POST` | `/api/tracking/{type}/{id}/delete` | Bulk delete; body is an array of uids |
| `GET` | `/api/tracking/all/{id}` | Every type for the active profile |
| `GET` | `/api/tracking/allProfiles/{id}` | Every type across all profiles, plus `profile` list |
| `GET` | `/api/tracking/pokemon/refresh` | Alias for `/api/reload` |

Query parameters: `profile_no` (act on another profile), `silent` or `suppressMessage` (any value — don't send the confirmation message). On `all`/`allProfiles`, `includeDescriptions` adds descriptions; it defaults to off for `all` and on for `allProfiles`.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/tracking/pokemon/123456789
```

```json
{
  "status": "ok",
  "pokemon": [
    {"uid": 42, "pokemon_id": 1, "form": 0, "min_iv": 90, "max_iv": 100, "distance": 500, "template": "1", "clean": 0,
     "description": "**Bulbasaur** | iv: 90%-100% | ..."}
  ]
}
```

The list key is the type name (`pokemon`, `raid`, `egg`, `quest`, `invasion`, `lure`, `nest`, `gym`, `fort`, `maxbattle`).

### Create

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/tracking/pokemon/123456789 \
  -d '[{"pokemon_id": 1, "min_iv": 90, "max_iv": 100, "distance": 500}]'
```

```json
{"status": "ok", "message": "New: **Bulbasaur** | iv: 90%-100% | ...", "newUids": [42], "alreadyPresent": 0, "updates": 0, "insert": 1}
```

If an incoming rule matches an existing one except for a single display field (`distance`, `template`, `clean`, `ping`), the existing rule is updated instead of duplicated. In 5.3+ the updated rule keeps its `uid` (as it does when edited through bot commands or v2), so tracking mutes, alert buttons and `id:N` commands that refer to it keep working; earlier builds replaced it under a new uid.

Rules use the stored representation directly: v1 sentinels (`9000` = any boss/move, `-1` = no floor, `4096` = no rank limit), integer enums (`team` 0–4 with 4 = any, `gender` 0–3), and the `clean` bitmask (1 = clean, 2 = edit, 4 = summary). Notable per-type fields:

| Type | Key fields |
|---|---|
| pokemon | `pokemon_id` (0 = all), `form`, `min_iv`/`max_iv`, `min_cp`/`max_cp`, `min_level`/`max_level`, `atk`/`def`/`sta` + `max_*`, `gender`, `size`/`max_size`, `rarity`/`max_rarity`, `pvp_ranking_league`, `pvp_ranking_best`/`worst`, `pvp_ranking_min_cp`, `pvp_ranking_cap`, `pvp_ranking_evolution` (0 base, 1 Mega, 2 Mega X, 3 Mega Y) |
| raid | `level` (int or array; 90 = all) **or** `pokemon_id` **or** `pokemon_form: [{pokemon_id, form}]`; `team`, `exclusive`, `move`, `evolution`, `gym_id`, `rsvp_changes` (0/1/2) |
| egg | `level`* (int or array; 90 = all), `team`, `exclusive`, `gym_id`, `rsvp_changes` |
| quest | `reward_type`* (2 item, 3 stardust, 4 candy, 7 pokemon, 8 pokecoins, 12 mega energy), `reward`, `form`, `shiny`, `amount` |
| invasion | `grunt_type` (name; empty = any — also used for event stops such as `showcase`), `gender` |
| lure | `lure_id` (0 = any, 501–506) |
| nest | `pokemon_id`, `form`, `min_spawn_avg` |
| gym | `team`*, `slot_changes`, `battle_changes`, `gym_id` |
| fort | `fort_type` (`pokestop`/`gym`/`everything`), `change_types`, `include_empty` (**defaults false in v1**) |
| maxbattle | `pokemon_id` (9000 = any), `level`, `form`, `move`, `gmax`, `evolution`, `station_id` |

All types also accept `distance`, `template`, `clean`, `ping`, `override_location_label` and `override_areas`. The override rules: a location label needs `distance > 0`; areas and distance are mutually exclusive; label and areas are mutually exclusive; the label must exist and areas must be ones the user may select. Violations return `400`.

### Delete

```sh
curl -X DELETE -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/tracking/pokemon/123456789/byUid/42

curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '[42, 43, 44]' http://localhost:3030/api/tracking/pokemon/123456789/delete
```

Both return `{"status":"ok","message":"…"}` with the removal confirmation text.

## Humans

| Method | Path | Body | Returns |
|---|---|---|---|
| `GET` | `/api/humans/one/{id}` | — | `{human}` — the full record |
| `GET` | `/api/humans/{id}` | — | `{areas}` — **areas available to the user**, not the user record |
| `POST` | `/api/humans` | `{id, name, type?, enabled?, area?, latitude?, longitude?, language?, admin_disable?, community?, profile_name?, notes?}` | `{message, human}`; `409` if it exists |
| `POST` | `/api/humans/{id}/start` | — | ok |
| `POST` | `/api/humans/{id}/stop` | — | ok |
| `POST` | `/api/humans/{id}/adminDisabled` | `{"state": true}` | `{admin_disabled}` |
| `POST` | `/api/humans/{id}/language` | `{"language": "de"}` | `{language}` |
| `POST` | `/api/humans/{id}/switchProfile/{profile}` | — | ok; `404` if no such profile |
| `POST` | `/api/humans/{id}/setLocation/{lat}/{lon}` | — | ok; `403` outside permitted areas |
| `GET` | `/api/humans/{id}/checkLocation/{lat}/{lon}` | — | `{locationOk}` |
| `POST` | `/api/humans/{id}/setAreas` | `["canterbury", "dover"]` | `{setAreas}` |
| `GET` | `/api/humans/{id}/locations` | — | `{locations: {default, named}}` |
| `GET` | `/api/humans/{id}/locations/{label}` | — | `{location}` |
| `POST` | `/api/humans/{id}/locations/add` | `{label, latitude, longitude}` or an array | `{results: [{label, error?}]}` |
| `POST` | `/api/humans/{id}/locations/{label}/delete` | — | ok; `409` if referenced by a rule |
| `GET` | `/api/humans/{id}/roles` | — | `{guilds}` |
| `POST` | `/api/humans/{id}/roles/add/{roleId}` | — | `{result}` |
| `POST` | `/api/humans/{id}/roles/remove/{roleId}` | — | `{result}` |
| `GET` | `/api/humans/{id}/getAdministrationRoles` | — | `{admin}` |

There is **no** v1 endpoint to list, update (`PATCH`) or delete humans; use [`GET`/`DELETE /api/v2/humans`](v2/humans.md) (5.3+).

A `409` from deleting a referenced location looks like:

```json
{"status": "error", "error": "location is referenced by tracking rules", "referencing_rules": [{"Type": "pokemon", "UID": 42}]}
```

(Note the capitalised keys in v1; v2 uses `type`/`uid`.)

## Profiles

| Method | Path | Body |
|---|---|---|
| `GET` | `/api/profiles/{id}` | — → `{profile: [...]}`, `active_hours` as a JSON string |
| `POST` | `/api/profiles/{id}/add` | `{name, active_hours?}` or an array |
| `POST` | `/api/profiles/{id}/update` | `[{profile_no, active_hours}]` (only `active_hours` can change) |
| `POST` | `/api/profiles/{id}/copy/{from}/{to}` | — copies rules from `from` into `to` |
| `DELETE` | `/api/profiles/{id}/byProfileNo/{profile_no}` | — deletes the profile and its rules |

`active_hours` entries are `{day, hours, mins}` with `day` 1–7 (ISO, Monday = 1); v1 also accepts zero-padded strings such as `"09"`.

## Messages

`POST /api/postMessage` is a legacy alias of `POST /api/deliverMessages`. Its body is an **array** of delivery jobs — see [Delivery, test & commands](messaging.md#deliver-pre-rendered-messages).
