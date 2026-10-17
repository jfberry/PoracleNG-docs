# Tracking (v2)

Tracking rules are sub-resources of a human. Read [Overview & conventions](index.md) first — especially "omit means any" and the response shapes.

`{type}` is one of:

| Type | What it tracks | Bot equivalent |
|---|---|---|
| `pokemon` | Wild spawns | `!track` |
| `raid` | Raid bosses | `!raid` |
| `egg` | Raid eggs | `!egg` |
| `quest` | Quest rewards | `!quest` |
| `invasion` | Team Rocket grunts / leaders | `!invasion` |
| `incident` | Pokestop events such as Showcases and Kecleon (v2 only; in v1 and the bot these are invasion rules) | `!invasion showcase` |
| `lure` | Lures | `!lure` |
| `nest` | Nests | `!nest` |
| `gym` | Gym team / slot / battle changes | `!gym` |
| `fort` | Pokestop / gym edits (name, location, new, removed, …) | `!fort` |
| `maxbattle` | Max battles (Dynamax / Gigantamax) | `!maxbattle` |

## Endpoints

| Method | Path | Purpose | Success body |
|---|---|---|---|
| `GET` | `/api/v2/humans/{id}/tracking` | Full snapshot: human, every type's rules, profiles, locations, summaries, mutes | see below |
| `GET` | `/api/v2/humans/{id}/tracking/{type}` | List one type | `{"rules": [...]}` |
| `POST` | `/api/v2/humans/{id}/tracking/{type}` | Create (or update) rules; body is an **array** | `{"created", "updated", "unchanged"}` |
| `DELETE` | `/api/v2/humans/{id}/tracking/{type}?uid=1,2,3` | Bulk delete | `{"deleted": [...]}` |
| `GET` | `/api/v2/humans/{id}/tracking/{type}/{uid}` | Fetch one rule | `{"rules": [<one>]}` |
| `PUT` | `/api/v2/humans/{id}/tracking/{type}/{uid}` | Full replace; body is **one** rule object | `{"updated": [<rule>]}` |
| `DELETE` | `/api/v2/humans/{id}/tracking/{type}/{uid}` | Delete one rule | `{"deleted": [<rule>]}` |

### Query parameters

| Parameter | Applies to | Meaning |
|---|---|---|
| `profile` | all | Profile number to act on. Defaults to the human's active profile. |
| `include_descriptions` | all | Add a human-readable `description` (in the human's language) to every returned rule. |
| `silent` | mutations | `true` = don't send the user a confirmation message. |
| `trusted` | `POST`, `PUT` (5.3+) | Allow `override_areas` to name fences that aren't `userSelectable`, for a client that has already authorised the user. Unknown fences and area-security community limits still apply. |
| `all_profiles` | snapshot only | Return rules from every profile; each rule then carries `profile_no`. |

Unknown query parameters are rejected with `422`.

## Create — `POST …/tracking/{type}`

The body is always an array, even for one rule:

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  "http://localhost:3030/api/v2/humans/123456789012345678/tracking/pokemon?include_descriptions=true" \
  -d '[
    {"pokemon_id": 149, "min_iv": 95, "gender": "female", "clean": true},
    {"pokemon_id": 384, "pvp_ranking_league": 1500, "pvp_ranking_best": 1, "pvp_ranking_worst": 5}
  ]'
```

```json
{
  "created": [
    {"uid": 9012, "pokemon_id": 149, "min_iv": 95, "gender": "female", "clean": true, "form": null, "max_iv": null, "distance": null, "description": "**Dragonite** | iv: 95%-100% | gender: female | clean"}
  ],
  "updated": [],
  "unchanged": [
    {"uid": 8811, "pokemon_id": 384, "pvp_ranking_league": 1500, "pvp_ranking_best": null, "pvp_ranking_worst": 5, "description": "…"}
  ]
}
```

(Abbreviated — every rule in a response carries all its fields, with `null` for those at their default.)

POST keeps the bot's de-duplication behaviour. Each incoming rule is compared with the human's existing rules of that type on the same profile:

- **identical** → reported in `unchanged`, nothing written;
- **differs only in one "display" field** (`distance`, `template`, `clean`/`edit`/`summary`, or `ping`) → the existing rule is updated and reported in `updated`, keeping its `uid` (5.3+; earlier builds returned a new uid);
- **anything else** → a new rule is inserted and reported in `created`.

An empty array is rejected with `422`.

## Replace — `PUT …/tracking/{type}/{uid}`

The body is a **single** rule object (not an array) that fully specifies the rule; any field you omit resets to its default. The uid comes from the path, not the body.

```sh
curl -X PUT -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/v2/humans/123456789012345678/tracking/raid/80921 \
  -d '{"pokemon_id": 150, "team": "mystic", "distance": 2000}'
```

The rule comes back in `updated` and **keeps its `uid`** (5.3+), so mutes on that rule (`!mute id:N`, mute buttons, the [mutes API](profiles.md#mutes)), `id:N` commands and buttons on earlier alerts keep working. Earlier builds gave the replaced rule a new uid. If moving the rule back onto its old uid fails, the request still succeeds, the response carries the new uid, and the failure is logged, so always take the uid from the response.

If the replacement would be identical to a *different* existing rule, the PUT is refused with `409` and nothing is changed.

## Delete

```sh
# one rule
curl -X DELETE -H "X-Poracle-Secret: your-secret" \
  http://localhost:3030/api/v2/humans/123456789012345678/tracking/raid/80921

# several rules of one type
curl -X DELETE -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/v2/humans/123456789012345678/tracking/pokemon?uid=9012,9013,9014"
```

Both return `{"deleted": [...]}` with the removed rules. A single delete of a uid that isn't this human's is `404`; bulk delete skips uids that don't belong to the human.

## Full snapshot — `GET …/tracking`

Replaces v1's `all/{id}` and `allProfiles/{id}` in one call.

```sh
curl -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/v2/humans/123456789012345678/tracking?all_profiles=true"
```

```json
{
  "human": {"id": "123456789012345678", "name": "Alice", "type": "discord:user", "enabled": true, "current_profile_no": 1, "area": ["downtown"], "...": "..."},
  "tracking": {
    "pokemon": [{"uid": 9012, "profile_no": 1, "pokemon_id": 149, "...": "..."}],
    "raid": [],
    "egg": [],
    "quest": [],
    "invasion": [],
    "incident": [],
    "lure": [],
    "nest": [],
    "gym": [],
    "fort": [],
    "maxbattle": []
  },
  "profiles": [{"uid": 1, "id": "123456789012345678", "profile_no": 1, "name": "Default", "area": ["downtown"], "latitude": 51.28, "longitude": 1.08, "active_hours": []}],
  "locations": {"default": {"latitude": 51.28, "longitude": 1.08}, "named": [{"label": "work", "latitude": 51.3, "longitude": 1.1}]},
  "summaries": [{"alert_type": "quest", "active_hours": [{"day": 1, "hours": 7, "mins": 30}]}],
  "mutes": []
}
```

`human` has the same typed shape as [`GET /api/v2/humans/{id}`](humans.md#fetch-a-human), `profiles` the same as [`GET …/profiles`](profiles.md#list), and each `summaries` entry is `{alert_type, active_hours}` with ISO-weekday `active_hours` entries (1 = Monday … 7 = Sunday).

!!! warning "Changed in 5.3"
    Before 5.3 the snapshot's `human` and `profiles` used the v1 encodings (`enabled` as `0`/`1`, `area` and profile `active_hours` as JSON-encoded strings), and each `summaries` entry also carried the human's `id`.

## Fields common to every type

| Field | Type | Notes |
|---|---|---|
| `uid` | int | Rule id. Present in every response; never sent in a body. |
| `distance` | int | Metres from the anchor location. Omit/`0` = match by the profile's **areas** instead. Values above `[tracking] max_distance` are clamped to it (5.3+); read the limit from [`/api/config/poracleWeb`](../config.md) `maxDistance`. |
| `template` | string | DTS template name; omit for the server default. |
| `clean` | bool | Delete the alert when it expires. Not on `fort`. |
| `edit` | bool | Edit the original alert in place when the same event is re-sent. Supported on `pokemon`, `raid`, `egg`, `lure`, `incident`; `true` is rejected with `422` on `gym`, `invasion`, `nest`, `maxbattle`, `quest` (5.3+). Not on `fort`. |
| `summary` | bool | Buffer into the [summary digest](../summaries.md) instead of sending immediately. Only quest summaries are delivered today. Not on `fort`. |
| `ping` | string | Text appended to the alert, usually role/user mentions (5.3+; earlier versions ignored it and a PUT erased pings set by the bot). Stored verbatim — `@everyone` in a channel rule will ping. |
| `override_location_label` | string | Use one of the human's [saved locations](humans.md#saved-locations) as the distance anchor. Requires `distance > 0`. |
| `override_areas` | string[] | Restrict this rule to these areas. Mutually exclusive with `distance > 0` and with `override_location_label`. Areas must be ones the human may select (or use `?trusted=true`). |

`clean`, `edit` and `summary` are three booleans in v2; they're stored as v1's `clean` bitmask (1, 2 and 4).

## Per-type fields

`*` = required. Everything else is optional and means "any" when omitted.

### pokemon

| Field | Type | Notes |
|---|---|---|
| `pokemon_id` | int | Pokédex id. Omit for every pokemon (5.3+; required in 5.2). |
| `form` | int | Form id |
| `costume` | int | Omit = any costume; `0` = no costume; `N` = that costume. Names from [`/api/masterdata/costumes`](../config.md#game-data). |
| `min_iv` / `max_iv` | int | 0–100 |
| `min_cp` / `max_cp` | int | 0–9000 |
| `min_level` / `max_level` | int | 0–55 |
| `atk` / `def` / `sta`, `max_atk` / `max_def` / `max_sta` | int | 0–15 |
| `gender` | string | `any`, `male`, `female`, `genderless` |
| `rarity` / `max_rarity` | int | 1–6 |
| `size` / `max_size` | int | 1–5 |
| `min_weight` / `max_weight` | int | grams |
| `min_time` | int | Minimum seconds left on the spawn |
| `pvp_ranking_league` | int | `500`, `1500` or `2500`. Omit for plain IV tracking. |
| `pvp_ranking_best` / `pvp_ranking_worst` | int | Rank range 1–4096 |
| `pvp_ranking_min_cp` | int | CP floor for PVP |
| `pvp_ranking_cap` | int | Level cap; omit for the league default |
| `pvp_ranking_evolution` | int | `0` base, `1` any Mega, `2` Mega X, `3` Mega Y |

### raid

| Field | Type | Notes |
|---|---|---|
| `pokemon_id` | int | A specific boss. Omit to track by `level` (any boss). |
| `level` | int | Raid tier; only used when `pokemon_id` is omitted. Omit both for every raid. One tier per rule — POST several objects for several tiers. |
| `form`, `costume`, `move`, `evolution` | int | |
| `team` | string | `harmony`, `mystic`, `valor`, `instinct`, `any` |
| `exclusive` | bool | EX raids only |
| `gym_id` | string | One gym |
| `rsvp_changes` | string | `none`, `rsvp` (include RSVP updates), `rsvp_only` |

### egg

| Field | Type | Notes |
|---|---|---|
| `level`* | int | Egg tier (one per rule) |
| `team`, `exclusive`, `gym_id`, `rsvp_changes` | | As for raid |

### quest

| Field | Type | Notes |
|---|---|---|
| `reward_type`* | int | `2` item, `3` stardust, `4` candy, `7` pokemon encounter, `8` pokecoins, `12` mega energy |
| `reward` | int | Item id, pokemon id, or stardust/pokecoin amount. Omit (or `0`) for every reward of that type — `{"reward_type": 2}` is "all items". |
| `amount` | int | Minimum amount (items, candy, mega energy) |
| `form` | int | For pokemon rewards |
| `shiny` | bool | Shiny-possible rewards only |

### invasion

Choose the grunt with **one** of these fields:

| Field | Type | Notes |
|---|---|---|
| `type_id` | int | Pokemon type 1–18 (e.g. `11` = water). May be combined with `gender` (`any`, `male`, `female`). |
| `grunt_id` | int | A specific grunt character from [`/api/masterdata/grunts`](../config.md#game-data); implies its gender. |
| `grunt_type` | string | (5.3+) The stored name directly: a type (`"water"`), a leader or named grunt (`"giovanni"`, `"cliff"`, `"mixed"`), or `"everything"` / `"boss"`. |
| `everything` | bool | Every invasion |
| `boss` | bool | Leaders / Giovanni only |

In 5.3+, omitting all of them also means "every invasion", and **reads always return `grunt_type`** (with `gender` where relevant) rather than echoing `type_id`/`grunt_id`. In 5.2 exactly one of `type_id` / `grunt_id` / `everything` / `boss` is required and reads echo the mode you used. Pokestop events are not invasions — use `incident`.

### incident

| Field | Type | Notes |
|---|---|---|
| `display_type`* | int | Pokestop event id from the game master (e.g. Showcase, Kecleon) |

### lure

| Field | Type | Notes |
|---|---|---|
| `lure_id`* | int | `0` any, `501` normal, `502` glacial, `503` mossy, `504` magnetic, `505` rainy, `506` sparkly. Required — send `0` explicitly for any. |

### nest

| Field | Type | Notes |
|---|---|---|
| `pokemon_id` | int | Omit for any species |
| `form` | int | |
| `min_spawn_avg` | int | Minimum spawns per hour |

### gym

| Field | Type | Notes |
|---|---|---|
| `team`* | string | `harmony`, `mystic`, `valor`, `instinct`, or `any` |
| `slot_changes` | bool | Alert on defender slot changes |
| `battle_changes` | bool | Alert on battles |
| `gym_id` | string | One gym |

### fort

| Field | Type | Notes |
|---|---|---|
| `fort_type` | string | `pokestop`, `gym` or `everything` (default) |
| `change_types` | string[] | Any of `location`, `new`, `removal`, `image_url`, `name`, `description`. Omit for all. |
| `include_empty` | bool | Include forts with no name/description. **Defaults to `true`** (v1 defaulted to `false`). |

`fort` has no `clean`, `edit` or `summary`.

### maxbattle

| Field | Type | Notes |
|---|---|---|
| `pokemon_id` | int | A specific boss; omit to track by `level` |
| `level` | int | Battle tier when tracking by level |
| `form`, `move`, `evolution` | int | |
| `gmax` | bool | Gigantamax only |
| `station_id` | string | One power spot |

## Examples

Any level-5 raid at a specific gym, editing the alert as RSVPs come in:

```json
[{"level": 5, "gym_id": "0a1b2c3d4e.16", "rsvp_changes": "rsvp", "edit": true}]
```

All stardust quests of at least 1000, and every pokemon encounter reward:

```json
[{"reward_type": 3, "reward": 1000}, {"reward_type": 7}]
```

Female water grunts, plus any leader/boss encounter:

```json
[{"type_id": 11, "gender": "female"}, {"boss": true}]
```

Showcase incidents restricted to two areas:

```json
[{"display_type": 9, "override_areas": ["downtown", "harbour"]}]
```

Field-level detail (bounds, defaults and the exact wildcard values stored) is in `/docs` under the **v2-tracking** tag.
