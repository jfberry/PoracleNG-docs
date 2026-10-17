# Migrating from v1

The v1 endpoints (`/api/tracking/*`, `/api/humans/*`, `/api/profiles/*`) are frozen: they keep working, but new features ship only in [v2](index.md). This page maps v1 calls to their v2 equivalents. Auth is unchanged — keep sending `X-Poracle-Secret`.

## Behaviour changes to plan for

1. **Strict input.** Unknown body fields and query parameters → `422`. No string-for-number or number-for-bool coercion.
2. **problem+json errors** instead of `{"status":"error","message":…}`.
3. **Omit means "any".** Stop sending sentinels (`9000`, `-1`, `4096`, …); reads return `null` for wildcards. GET → PUT round-trips unchanged.
4. **Create bodies are always arrays.** PUT bodies are a single object.
5. **`?silent=true`** replaces `silent` / `suppressMessage`; **`?profile=`** replaces `profile_no`; **`?include_descriptions=true`** replaces `includeDescriptions` and works on every tracking call.
6. **No `{"status":"ok"}` envelope** on reads or tracking mutations.
7. **PUT replaces in full.** Omitted fields reset to their defaults. In 5.3+ the rule keeps its `uid` (earlier builds returned a new one).
8. **Typed human and profile records** (5.3+). `enabled`/`admin_disable` are booleans, `area`, `community_membership`, `blocked_alerts` and profile `area` are arrays, `area_restriction` is an array or `null`, and `language` is `""` when unset. v1 still returns `0`/`1` and JSON-encoded strings.

## Tracking

| v1 | v2 |
|---|---|
| `GET /api/tracking/{type}/{id}` | `GET /api/v2/humans/{id}/tracking/{type}` → `{rules}` |
| `POST /api/tracking/{type}/{id}` (object or array) | `POST /api/v2/humans/{id}/tracking/{type}` (array) → `{created, updated, unchanged}` |
| — | `GET` / `PUT /api/v2/humans/{id}/tracking/{type}/{uid}` |
| `DELETE /api/tracking/{type}/{id}/byUid/{uid}` | `DELETE /api/v2/humans/{id}/tracking/{type}/{uid}` → `{deleted}` |
| `POST /api/tracking/{type}/{id}/delete` (body `[uids]`) | `DELETE /api/v2/humans/{id}/tracking/{type}?uid=1,2,3` |
| `GET /api/tracking/all/{id}` | `GET /api/v2/humans/{id}/tracking` |
| `GET /api/tracking/allProfiles/{id}` | `GET /api/v2/humans/{id}/tracking?all_profiles=true` |
| `GET /api/tracking/pokemon/refresh` | `GET`/`POST /api/reload` |
| Showcase/Kecleon etc. as invasion rules | New `incident` type, keyed by `display_type` |

## Humans

| v1 | v2 |
|---|---|
| — | `GET /api/v2/humans[?type=&id=]` (5.3+) |
| `POST /api/humans` | `POST /api/v2/humans` |
| `GET /api/humans/one/{id}` | `GET /api/v2/humans/{id}` |
| — | `DELETE /api/v2/humans/{id}` (5.3+) |
| `GET /api/humans/{id}` (available areas) | `GET /api/v2/humans/{id}/areas` |
| `POST /api/humans/{id}/start` / `stop` | `POST /api/v2/humans/{id}/enable` / `disable` |
| `POST /api/humans/{id}/adminDisabled` `{state}` | `POST /api/v2/humans/{id}/admin-disable` `{disabled}` |
| `POST /api/humans/{id}/language` | `POST /api/v2/humans/{id}/language` |
| `POST /api/humans/{id}/setLocation/{lat}/{lon}` | `POST /api/v2/humans/{id}/location` `{latitude, longitude}` |
| — | `DELETE /api/v2/humans/{id}/location` (5.3+) |
| `GET /api/humans/{id}/checkLocation/{lat}/{lon}` | `GET /api/v2/humans/{id}/check-location?latitude=&longitude=` |
| `POST /api/humans/{id}/setAreas` (body `[names]`) | `POST /api/v2/humans/{id}/areas` `{areas}` |
| `POST /api/humans/{id}/switchProfile/{n}` | `POST /api/v2/humans/{id}/profile` `{profile_no}` |
| `GET /api/humans/{id}/locations[/{label}]` | `GET /api/v2/humans/{id}/locations[/{label}]` |
| `POST /api/humans/{id}/locations/add` | `POST /api/v2/humans/{id}/locations` `{label, latitude, longitude}` (one per call) |
| — | `PUT /api/v2/humans/{id}/locations/{label}` `{latitude, longitude}` |
| `POST /api/humans/{id}/locations/{label}/delete` | `DELETE /api/v2/humans/{id}/locations/{label}` |
| `GET /api/humans/{id}/roles` | `GET /api/v2/humans/{id}/roles` |
| `POST /api/humans/{id}/roles/add/{roleId}` | `POST /api/v2/humans/{id}/roles/{roleId}` |
| `POST /api/humans/{id}/roles/remove/{roleId}` | `DELETE /api/v2/humans/{id}/roles/{roleId}` |
| `GET /api/humans/{id}/getAdministrationRoles` | `GET /api/v2/humans/{id}/admin-roles` |
| — | `GET` / `POST` / `DELETE /api/v2/humans/{id}/mutes` |

## Profiles

| v1 | v2 |
|---|---|
| `GET /api/profiles/{id}` | `GET /api/v2/humans/{id}/profiles` (`active_hours` typed) |
| `POST /api/profiles/{id}/add` | `POST /api/v2/humans/{id}/profiles` (one per call) |
| `POST /api/profiles/{id}/update` | `PATCH /api/v2/humans/{id}/profiles/{profile_no}` |
| `DELETE /api/profiles/{id}/byProfileNo/{n}` | `DELETE /api/v2/humans/{id}/profiles/{n}` |
| `POST /api/profiles/{id}/copy/{from}/{to}` | `POST /api/v2/humans/{id}/profiles/{to}/copy` `{from_profile}` |

## Field changes

### `clean` bitmask → booleans

| v1 `clean` | v2 |
|---|---|
| `1` | `"clean": true` |
| `2` | `"edit": true` |
| `3` | `"clean": true, "edit": true` |
| `4` | `"summary": true` |

### Enums

| Field | v1 | v2 |
|---|---|---|
| `team` | `0`–`4` | `harmony`, `mystic`, `valor`, `instinct`, `any` |
| `gender` | `0`–`3` | `any`, `male`, `female`, `genderless` |
| `rsvp_changes` | `0`–`2` | `none`, `rsvp`, `rsvp_only` |
| `fort_type` | string | `pokestop`, `gym`, `everything` (now enforced) |

### Sentinels to stop sending

| v1 | Meaning | v2 |
|---|---|---|
| `min_iv: -1`, `rarity: -1`, `size: -1` | no floor | omit |
| `max_iv: 100`, `max_level: 55`, `max_atk/def/sta: 15`, `max_rarity: 6`, `max_size: 5`, `max_cp: 9000` | no ceiling | omit |
| `pvp_ranking_worst: 4096`, `max_weight: 9000000` | no limit | omit |
| raid/maxbattle `pokemon_id: 9000` | track by level | omit `pokemon_id` |
| raid/maxbattle `level: 90` | all tiers | omit `level` |
| `form: 0`, `pvp_ranking_league: 0` | any / off | omit |

`distance: 0` still means "use areas", not a sentinel to avoid.

### Per type

- **raid / egg** — v1 accepted `level` as an array and a `pokemon_form` array; v2 takes one `level` / one `pokemon_id` per rule object — send several objects.
- **quest** — `reward_type` is required: `2` item, `3` stardust, `4` candy, `7` pokemon, `8` pokecoins, `12` mega energy.
- **invasion** — choose with `type_id`, `grunt_id`, `everything` or `boss` (and `grunt_type` in 5.3+). Event stops moved to `incident`.
- **fort** — `include_empty` defaults to `true` when omitted (v1: `false`).
- **all types** — `distance` is clamped to `[tracking] max_distance` (5.3+; v1 doesn't clamp). `ping` is accepted (5.3+).
- **`active_hours`** — typed array of `{day 1–7 (ISO, Monday = 1), hours, mins, step?, end_hours?, end_mins?}`, strict integers, no cross-midnight ranges. v1 stored any JSON string.

## Suggested order

1. Switch reads to the snapshot (`GET /api/v2/humans/{id}/tracking`) — it replaces `all`, `allProfiles`, and separate profile/location fetches.
2. Move tracking writes type by type, removing sentinel handling.
3. Move human/profile/location actions (mostly mechanical renames).
4. Handle problem+json errors once for all of `/api`.
