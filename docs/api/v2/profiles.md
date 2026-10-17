# Profiles, roles & mutes (v2)

See [Overview & conventions](index.md) for authentication, strictness and error handling. User-facing behaviour of these features is described in [Profiles](../../userguide/profiles.md) and [Muting Alerts](../../userguide/mute.md).

## Profiles

A profile is a separate set of tracking rules, areas and location. Exactly one profile is active at a time; tracking endpoints act on it unless given `?profile=`. A profile can switch itself on automatically on a schedule (`active_hours`).

| Method | Path | Purpose | Body |
|---|---|---|---|
| `GET` | `/api/v2/humans/{id}/profiles` | List profiles | — |
| `POST` | `/api/v2/humans/{id}/profiles` | Create a profile | `{name, active_hours?}` |
| `PATCH` | `/api/v2/humans/{id}/profiles/{profile_no}` | Rename and/or reschedule | `{name?, active_hours?}` |
| `DELETE` | `/api/v2/humans/{id}/profiles/{profile_no}` | Delete a profile and its rules | — |
| `POST` | `/api/v2/humans/{id}/profiles/{profile_no}/copy` | Copy rules **into** `profile_no` | `{from_profile}` |
| `POST` | `/api/v2/humans/{id}/profile` | Switch the active profile | `{profile_no}` |

### List

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/profiles
```

```json
{
  "profiles": [
    {"uid": 41, "id": "123456789012345678", "profile_no": 1, "name": "Default", "area": ["downtown"], "latitude": 51.28, "longitude": 1.08, "active_hours": []},
    {"uid": 42, "id": "123456789012345678", "profile_no": 2, "name": "Work", "area": ["business-park"], "latitude": 51.30, "longitude": 1.07,
     "active_hours": [{"day": 1, "hours": 8, "mins": 30}, {"day": 2, "hours": 8, "mins": 30}]}
  ]
}
```

`active_hours` is a typed array (`[]` when unscheduled) and `area` is an array of area names (`[]` when the profile has none). Before 5.3, `area` was a JSON-encoded string.

### `active_hours`

Each entry is a time at which this profile becomes active:

| Field | Type | Range | Notes |
|---|---|---|---|
| `day`* | int | 1–7 | ISO weekday: **1 = Monday … 7 = Sunday** |
| `hours`* | int | 0–23 | |
| `mins`* | int | 0–59 | |
| `step` | int | ≥ 0 | Hours between repeats. `> 0` makes this a range entry. |
| `end_hours` / `end_mins` | int | 0–23 / 0–59 | Required when `step > 0`. The range can't cross midnight. |

`{"day": 6, "hours": 9, "mins": 0, "step": 3, "end_hours": 18, "end_mins": 0}` fires Saturday at 09:00, 12:00, 15:00 and 18:00.

Values must be real integers (v1 accepted `"09"`). Invalid schedules are rejected with `422`.

### Create

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"name": "Work", "active_hours": [{"day": 1, "hours": 8, "mins": 30}]}' \
  http://localhost:3030/api/v2/humans/123456789012345678/profiles
```

The server picks the **lowest free** profile number. In 5.3+ the response tells you which:

```json
{"profile_no": 2, "profile": {"uid": 42, "profile_no": 2, "name": "Work", "...": "..."}}
```

(5.2 returns `{"status":"ok"}`; list the profiles afterwards to find the number.)

### Update

```sh
curl -X PATCH -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"active_hours": []}' \
  http://localhost:3030/api/v2/humans/123456789012345678/profiles/2
```

Send `active_hours`, `name` (5.3+), or both; omitted fields are unchanged. `[]` clears the schedule.

### Switch, copy, delete

```sh
# make profile 2 active (also copies its areas/location onto the human)
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"profile_no": 2}' http://localhost:3030/api/v2/humans/123456789012345678/profile

# copy every rule from profile 1 into profile 3, REPLACING profile 3's rules
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"from_profile": 1}' http://localhost:3030/api/v2/humans/123456789012345678/profiles/3/copy

# delete profile 2 and its rules
curl -X DELETE -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/profiles/2
```

All three return `{"status":"ok"}`. Deleting the active profile makes another profile active.

!!! info "Profile 0 (5.3+)"
    Older builds registered some humans (via `!poracle`, role reconciliation, `!channel add`, `!webhook add`) on a non-existent profile 0, so rules saved by API clients to profile 1 never matched. Database migration 000009 moves those humans and their rules onto profile 1, and new humans always start on profile 1.

## Discord subscription roles

These manage the self-service roles configured in `[[discord.role_subscriptions]]`. They require the Discord bot to be running (`503` otherwise) and only apply to `discord:user` humans.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/v2/humans/{id}/roles` | Subscribable roles per guild, with `set` showing which the user has |
| `POST` | `/api/v2/humans/{id}/roles/{roleId}` | Give the user a role |
| `DELETE` | `/api/v2/humans/{id}/roles/{roleId}` | Remove a role |
| `GET` | `/api/v2/humans/{id}/admin-roles` | What the user may administer (delegated administration) |

```json
{
  "guilds": [
    {"name": "My Server", "roles": {
      "general": [{"id": "987654321", "description": "Raid pings", "set": true}],
      "exclusive": [[{"id": "111", "description": "Team Mystic", "set": false}, {"id": "112", "description": "Team Valor", "set": true}]]
    }}
  ]
}
```

Add/remove return `{"result": [...]}` with the role changes made. `admin-roles` returns:

```json
{"admin": {"discord": {"channels": ["111222333"], "webhooks": [], "users": false}, "telegram": {"channels": [], "users": false}}}
```

## Mutes

Mutes temporarily silence a human's alerts — the same mutes created by `!mute` and alert buttons. They are held **in memory only** and are cleared when the processor restarts.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/v2/humans/{id}/mutes` | Active mutes |
| `POST` | `/api/v2/humans/{id}/mutes` | Create (or extend) a mute |
| `DELETE` | `/api/v2/humans/{id}/mutes?scope=&value=` | Remove one mute |
| `DELETE` | `/api/v2/humans/{id}/mutes` | Remove every mute |

### Scopes

| `scope` | `value` |
|---|---|
| `pokemon` | Pokédex id, as a string (`"149"`) |
| `gym` | Gym id |
| `pokestop` | Pokestop id |
| `station` | Power-spot station id |
| `area` | Geofence name (case-insensitive; must exist) |
| `tracking` | A tracking rule uid, as a string |
| `everything` | Must be omitted |

### Create

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  -d '{"scope": "area", "value": "downtown", "duration_min": 120}' \
  http://localhost:3030/api/v2/humans/123456789012345678/mutes
```

```json
{
  "mute": {"scope": "area", "value": "downtown", "expires_at": 1792351200, "remaining_secs": 7200},
  "replaced": false
}
```

`duration_min` defaults to 60 and is capped at 10080 (one week). Muting the same scope and value again extends the existing mute and returns `"replaced": true`.

### List and delete

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/v2/humans/123456789012345678/mutes
# {"mutes": [{"scope": "everything", "value": null, "expires_at": 1792348000, "remaining_secs": 4000}]}

curl -X DELETE -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/v2/humans/123456789012345678/mutes?scope=pokemon&value=149"
```

Delete returns `{"deleted": [...]}` with the removed mutes. A targeted delete of a mute that doesn't exist is `404`; delete-all with nothing to remove returns an empty list. The [full tracking snapshot](tracking.md#full-snapshot-get-tracking) also includes a `mutes` array.
