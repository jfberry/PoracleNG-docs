# API v2 — Overview & conventions

`/api/v2` is the strict, typed API for managing users ("humans"), their tracking rules, profiles, saved locations, roles and mutes. It replaces the frozen [v1 endpoints](../humans.md) and is the recommended API for new clients. Every endpoint and field is described in the OpenAPI spec at `GET /openapi.json` and the interactive docs at `GET /docs` on your processor.

Authentication is the same as the rest of the API: send `X-Poracle-Secret` (see [API overview](../index.md#authentication)).

## Resource layout

Everything belongs to a human, so everything lives under one prefix:

```
/api/v2/humans                                   list, create
/api/v2/humans/{id}                              fetch, delete
/api/v2/humans/{id}/tracking                     full snapshot
/api/v2/humans/{id}/tracking/{type}              list, create, bulk delete
/api/v2/humans/{id}/tracking/{type}/{uid}        fetch, replace, delete one rule
/api/v2/humans/{id}/profiles[/{profile_no}]      profiles
/api/v2/humans/{id}/locations[/{label}]          saved locations
/api/v2/humans/{id}/roles[/{roleId}]             Discord subscription roles
/api/v2/humans/{id}/mutes                        alert mutes
/api/v2/activity                                 recently-seen game data (5.3+)
```

`{id}` is the Discord user/channel id, Telegram chat id, or webhook name. Every operation is scoped to that human; you can't read or modify a rule uid that belongs to someone else (you get `404`).

| Page | Covers |
|---|---|
| [Tracking](tracking.md) | The 11 tracking types, snapshot, create/replace/delete, field conventions |
| [Humans & locations](humans.md) | Listing, creating, deleting humans; enable/disable; language; areas; location; saved locations |
| [Profiles, roles & mutes](profiles.md) | Profiles and `active_hours`, profile switching and copying, Discord roles, mutes |
| [Migrating from v1](migration.md) | v1 → v2 endpoint and field mapping |

!!! warning "Changes in 5.3 (breaking for v2 clients)"
    In 5.3 v2 stopped reusing v1's wire format for humans and profiles. If you wrote a client against an earlier v2 build:

    - **Human** (`GET /api/v2/humans/{id}`, `POST /api/v2/humans`, snapshot `human`): `enabled` and `admin_disable` are booleans (were `1`/`0`). `area`, `community_membership` and `blocked_alerts` are arrays, `[]` when empty (were JSON-encoded strings, and `blocked_alerts` could be `null`). `area_restriction` is an array or `null` (`null` = unrestricted, `[]` = no areas allowed). `language` is `""` when unset (was `null`).
    - **Profiles** (`GET …/profiles` and snapshot `profiles`): `area` is an array. The snapshot's profiles now return `active_hours` as a typed array too, like `GET …/profiles`.
    - **Snapshot `summaries`**: each entry is `{alert_type, active_hours}` with typed ISO-weekday entries; the `id` field is gone.
    - **Coordinates are `latitude`/`longitude` everywhere**: the bodies of `POST …/location`, `POST …/locations` and `PUT …/locations/{label}`, and the `check-location` query, no longer accept `lat`/`lon`.
    - **`POST /api/v2/humans`**: `area` is an array of area names (was a JSON-encoded string).
    - **Edited rules keep their `uid`**: `PUT …/tracking/{type}/{uid}` and a `POST` that updates an existing rule return the rule under its existing uid (they used to return a new one). The same applies to v1 `POST /api/tracking/*` and bot commands.

    v1 endpoints are unchanged.

## Conventions

### Strict input

- Request bodies are validated against the schema. **Unknown fields are rejected** with `422`, as are wrong types (`"90"` is not `90`, `1` is not `true`).
- Most v2 endpoints also **reject unknown query parameters** with `422`.
- Field names are `snake_case`.
- Game-master ids stay integers (`pokemon_id`, `form`, `move`, `reward_type`, `lure_id`, `grunt_id`, `display_type`, …). Small fixed categories are strings (`team`, `gender`, `fort_type`, `rsvp_changes`).

### Omit means "any"

To leave a filter unconstrained, **omit the field**. Don't send v1's sentinel numbers (`9000`, `-1`, `4096`, …) — the server fills them in. Reads return `null` for any filter that's at its "any"/default value, so a rule shows only the filters that matter:

```json
{"uid": 812, "pokemon_id": 149, "min_iv": 90, "max_iv": null, "form": null, "distance": null, "clean": true}
```

Because `null` and an omitted field mean the same thing on input, you can **GET a rule and PUT it straight back** without changing it.

`distance: 0` (or omitted) is not "zero metres": it means "match by the profile's geofence areas instead of a radius".

### Profiles

Tracking, location and area endpoints act on the human's **active profile** unless you pass `?profile=N`. The full snapshot also accepts `?all_profiles=true`.

### Confirmation messages

By default a tracking mutation sends the user the same "added/updated/removed" confirmation the bot would. Pass `?silent=true` to apply the change without messaging them.

### Response shapes

| Kind of endpoint | Success body |
|---|---|
| Reads | The resource directly, e.g. `{"rules": [...]}`, `{"human": {...}}`, `{"profiles": [...]}` |
| Tracking create / replace | `{"created": [...], "updated": [...], "unchanged": [...]}` |
| Tracking delete | `{"deleted": [...]}` |
| Pure actions (enable, language, location, profile switch, …) | `{"status": "ok"}` |

There's no `{"status":"ok"}` wrapper on reads or tracking mutations.

### Errors

Errors are RFC 9457 `application/problem+json`:

```json
{
  "title": "Unprocessable Entity",
  "status": 422,
  "detail": "validation failed",
  "errors": [
    {"message": "expected required property reward_type to be present", "location": "body[0]", "value": {}}
  ]
}
```

| Status | Typical cause |
|---|---|
| `401` | Missing/wrong `X-Poracle-Secret` (legacy `{"status":"authError"}` body) |
| `403` | Location outside the human's permitted area (`POST …/location`) |
| `404` | Human, rule, profile, saved location or mute doesn't exist (or isn't this human's) |
| `409` | Duplicate (human already exists, location label taken, PUT would duplicate another rule) or a saved location still referenced by a rule |
| `422` | Schema validation, unknown field/parameter, or a business rule (e.g. `edit: true` on a type that doesn't support it) |
| `503` | A dependency isn't running (e.g. Discord bot for role endpoints) |

### Changes take effect immediately

Every v2 mutation triggers a debounced state reload, so new or changed rules start matching within moments. You don't need to call `/api/reload`.

## Quick example

Create a human, track 100% Dratini within 1 km of their location, then read everything back:

```sh
SECRET="your-secret"
BASE=http://localhost:3030/api/v2

curl -X POST -H "X-Poracle-Secret: $SECRET" -H "Content-Type: application/json" \
  -d '{"id": "123456789012345678", "name": "Alice", "type": "discord:user"}' \
  $BASE/humans

curl -X POST -H "X-Poracle-Secret: $SECRET" -H "Content-Type: application/json" \
  -d '{"latitude": 51.2802, "longitude": 1.0789}' \
  $BASE/humans/123456789012345678/location

curl -X POST -H "X-Poracle-Secret: $SECRET" -H "Content-Type: application/json" \
  -d '[{"pokemon_id": 147, "min_iv": 100, "distance": 1000}]' \
  "$BASE/humans/123456789012345678/tracking/pokemon?silent=true"

curl -H "X-Poracle-Secret: $SECRET" \
  "$BASE/humans/123456789012345678/tracking?include_descriptions=true"
```
