# Humans & locations (v2)

A **human** is any alert destination: a Discord user, channel or thread, a Telegram user/group/channel, or a webhook. Its `id` is the platform id (or the webhook name). See [Overview & conventions](index.md) for strictness, errors and response shapes.

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/v2/humans` | List humans (5.3+) |
| `POST` | `/api/v2/humans` | Create a human |
| `GET` | `/api/v2/humans/{id}` | Fetch one human |
| `DELETE` | `/api/v2/humans/{id}` | Delete a human and all their data (5.3+) |
| `POST` | `/api/v2/humans/{id}/enable` | Turn alerts on (`!start`) |
| `POST` | `/api/v2/humans/{id}/disable` | Turn alerts off (`!stop`) |
| `POST` | `/api/v2/humans/{id}/admin-disable` | Set or clear the admin-disable flag |
| `POST` | `/api/v2/humans/{id}/language` | Set alert/bot language |
| `GET` | `/api/v2/humans/{id}/areas` | Areas this human may select |
| `POST` | `/api/v2/humans/{id}/areas` | Set selected areas |
| `POST` | `/api/v2/humans/{id}/location` | Set location |
| `DELETE` | `/api/v2/humans/{id}/location` | Clear location (5.3+) |
| `GET` | `/api/v2/humans/{id}/check-location?lat=&lon=` | Would this location be allowed? |
| `GET` / `POST` | `/api/v2/humans/{id}/locations` | List / create saved locations |
| `GET` / `PUT` / `DELETE` | `/api/v2/humans/{id}/locations/{label}` | Fetch / move / delete a saved location |

Profiles, roles and mutes are on [Profiles, roles & mutes](profiles.md).

## List humans (5.3+)

```sh
curl -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/v2/humans?type=discord:channel"
```

| Query | Meaning |
|---|---|
| `type` | Only this registration type (`discord:user`, `discord:channel`, `webhook`, `telegram:user`, …) |
| `id` | Comma-separated ids to fetch (`id=123,456`). Unknown ids are left out rather than erroring. |

Combining both intersects them. Omit both to list everyone.

```json
{
  "humans": [
    {
      "id": "111222333444555666",
      "type": "discord:channel",
      "name": "raid-alerts",
      "enabled": true,
      "admin_disable": false,
      "language": "en",
      "current_profile_no": 1,
      "last_checked": "2026-10-16T21:04:11Z",
      "disabled_date": null,
      "notes": "EU raids"
    }
  ]
}
```

This is a summary per human; fetch `GET /api/v2/humans/{id}` for the full record.

## Create a human

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/v2/humans \
  -d '{"id": "123456789012345678", "name": "Alice", "type": "discord:user", "language": "de", "community": ["kent"]}'
```

| Field | Notes |
|---|---|
| `id`*, `name`* | Required |
| `type` | Default `discord:user` |
| `enabled` | Default `true` |
| `admin_disable` | Default `false` |
| `language` | Default: the server's `[general] locale` |
| `area` | Initial areas as a **JSON-encoded string**, e.g. `"[\"downtown\"]"` |
| `latitude`, `longitude` | Initial location |
| `community` | Community names; with area security on, sets the human's area restriction |
| `profile_name` | Name of the default profile (default `Default`) |
| `notes` | Free-form admin notes |

Returns `{"human": {...}}` (the full record below). `409` if the id already exists. A default profile 1 is created alongside.

## Fetch a human

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678
```

```json
{
  "human": {
    "id": "123456789012345678",
    "type": "discord:user",
    "name": "Alice",
    "enabled": 1,
    "area": "[\"downtown\",\"harbour\"]",
    "latitude": 51.2802,
    "longitude": 1.0789,
    "fails": 0,
    "last_checked": null,
    "language": "de",
    "admin_disable": 0,
    "disabled_date": null,
    "current_profile_no": 1,
    "community_membership": "[\"kent\"]",
    "area_restriction": null,
    "notes": "",
    "blocked_alerts": null
  }
}
```

!!! note "Legacy encodings on the human record"
    The full human record keeps the database encodings: `enabled` and `admin_disable` are `0`/`1`, and `area`, `community_membership`, `area_restriction` and `blocked_alerts` are **JSON-encoded string arrays** that you must parse. `blocked_alerts` is derived from `command_security` during reconciliation and can't be set through the API.

## Delete a human (5.3+)

```sh
curl -X DELETE -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678
```

Removes the human, every tracking rule, profile and summary schedule they own. Irreversible. Returns `{"status":"ok"}`; `404` if they don't exist.

## Enable, disable, admin-disable

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/disable

curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"disabled": true}' \
  http://localhost:3030/api/v2/humans/123456789012345678/admin-disable
```

`enable`/`disable` take no body. `admin-disable` takes `{"disabled": bool}` and only flips the flag (it doesn't reset `enabled`, `fails` or `disabled_date`). An admin-disabled user can't re-enable themselves and must re-register.

## Language

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"language": "de"}' \
  http://localhost:3030/api/v2/humans/123456789012345678/language
```

If `[general.available_languages]` is configured, the code must be one of those (case-insensitive); otherwise it must be a language the server has translations for. `422` otherwise. Codes are stored lowercased.

## Areas

### Available areas

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/areas
```

```json
{
  "areas": [
    {"name": "Canterbury", "group": "Kent", "description": "", "userSelectable": true},
    {"name": "Dover", "group": "Kent", "description": "Port area", "userSelectable": true}
  ]
}
```

Filtered by community membership when [area security](../../configuration/areasecurity.md) is on.

### Set areas

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"areas": ["Canterbury", "dover", "atlantis"]}' \
  http://localhost:3030/api/v2/humans/123456789012345678/areas
```

5.3+ response:

```json
{"areas": ["canterbury", "dover"], "rejected": ["atlantis"]}
```

- Names are matched case-insensitively against fences this human may select (`userSelectable` fences for non-admins, filtered by community under area security), then stored lowercased and de-duplicated.
- Anything not stored is returned in `rejected` (5.3+). In 5.2 the endpoint returns `{"status":"ok"}` and silently drops unknown names.
- Body field `"trusted": true` (5.3+) also allows non-`userSelectable` fences, for a server-side client that has already authorised the user (e.g. they drew the fence themselves). Unknown fences are still rejected.
- Writes the active profile by default. `?profile=N` targets another profile; `?all_profiles=true` writes every profile (useful when a fence has been deleted). The two are mutually exclusive. (5.3+)

## Location

### Set

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"lat": 51.2802, "lon": 1.0789}' \
  http://localhost:3030/api/v2/humans/123456789012345678/location
```

Coordinates go in the body (v1 put them in the path). With area security on and an area restriction set, the point must lie inside an allowed fence, otherwise `403`. `?profile=N` writes another profile's location; in 5.3+ that no longer overwrites the human's live location unless N is the active profile.

### Clear (5.3+)

```sh
curl -X DELETE -H "X-Poracle-Secret: your-secret" \
  http://localhost:3030/api/v2/humans/123456789012345678/location
```

Resets the location to unset (0,0). Not subject to the area-restriction check. Rules with `distance > 0` stay as they are and won't match until a location is set again.

### Check

```sh
curl -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/v2/humans/123456789012345678/check-location?lat=51.28&lon=1.08"
```

```json
{"locationOk": true}
```

Always `true` when area security is off.

## Saved locations

Saved (named) locations let a tracking rule measure distance from somewhere other than the profile location, via `override_location_label` (see [Tracking](tracking.md#fields-common-to-every-type) and the [`!location` command](../../userguide/commands.md)). Labels are unique per human and matched case-insensitively.

!!! note "Field names"
    Requests use `lat`/`lon`; responses use `latitude`/`longitude`.

```sh
# list
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/locations
```

```json
{
  "locations": {
    "default": {"latitude": 51.2802, "longitude": 1.0789},
    "named": [{"label": "work", "latitude": 51.2961, "longitude": 1.0612}]
  }
}
```

`default` is the human's own location and is omitted when unset.

```sh
# create (409 if the label exists)
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"label": "work", "lat": 51.2961, "lon": 1.0612}' \
  http://localhost:3030/api/v2/humans/123456789012345678/locations

# fetch one -> {"location": {"label": "work", "latitude": ..., "longitude": ...}}
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/locations/work

# move (new in v2)
curl -X PUT -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"lat": 51.3001, "lon": 1.0700}' \
  http://localhost:3030/api/v2/humans/123456789012345678/locations/work

# delete
curl -X DELETE -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/locations/work
```

Deleting a location that a tracking rule still references fails with `409` and lists the rules, so you can repoint or delete them first:

```json
{
  "title": "Conflict",
  "status": 409,
  "detail": "location is referenced by one or more tracking rules",
  "referencing_rules": [{"type": "pokemon", "uid": 9012}]
}
```
