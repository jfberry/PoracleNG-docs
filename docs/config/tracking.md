# `[tracking]`

Restrictions and defaults for what users can track, plus alert edit and quest summary behaviour.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `everything_flag_permissions` | string | `"allow-any"` | How the `everything` flag is handled by `!track`. See below. |
| `default_distance` | int | `0` | Default tracking radius (metres) for distance-only tracking with no areas. |
| `max_distance` | int | `0` | Max tracking circle radius (metres). `0` = no limit. |
| `enable_gym_battle` | bool | `false` | Allow the `battle_changes` keyword in `!gym`. A gym sends a webhook every time it is attacked, so this can be very chatty. While `false`, `!gym battle_changes` is rejected. Only the command is gated; the tracking API and web editors can still set `battle_changes`. |
| `default_user_tracking_level_cap` | int | `0` | Default PVP tracking level cap. `0` = all configured caps. |
| `pokemon_change_tracking` | bool | `true` | When a pokemon changes after the first alert (IV reveal, form, species, gender, weather boost), send the update as a reply in a thread under the user's earlier alert, using the `monsterChanged` template. When `false`, changes are sent as a normal `monster` alert. |
| `pokemon_edit` | bool | `true` | **5.3+.** Honour the `edit` flag on pokemon rules: an update for the same encounter edits the original alert in place while it is within `pokemon_edit_window_mins`. When `false`, pokemon rules behave as if `edit` were not set and updates arrive as threaded replies. |
| `pokemon_edit_window_mins` | int | `5` | **5.3+.** Only edit a pokemon alert while it is at most this many minutes old. After that, updates arrive as threaded replies so they aren't lost further up a busy channel. `0` = no limit. |
| `default_edit` | array | `[]` | **5.3+.** Alert types that edit the original alert in place even when the rule doesn't set `edit`. Supported (case-insensitive): `pokemon`, `raid`, `egg`, `lure`, `incident`. Applies to existing and new rules while listed; remove a type to revert. Unsupported entries are ignored. |
| `quest_summary_enabled` | bool | `true` | Enable [quest summaries](../userguide/summaries.md). When `false`, the summary scheduler doesn't run. Buffered entries are still saved across restarts, so turning it back on loses nothing. Requires a restart. |
| `quest_summary_buffer_ttl_hours` | int | `24` | Safety limit on how long a buffered quest can wait for a summary. Quests normally expire at their own expiry time; this also drops entries older than this many hours, in case of bad expiry data. `0` = turn the age check off. |

```toml
[tracking]
pokemon_edit = true
pokemon_edit_window_mins = 5
default_edit = ["pokemon", "raid", "egg"]
```

## `everything_flag_permissions`

- **`allow-any`**: unrestricted; recorded as a wildcard. Users may also opt to use individual rows.
- **`allow-and-always-individually`**: unrestricted, but always recorded as one row per pokemon.
- **`allow-and-ignore-individually`**: unrestricted, but users cannot opt for individual rows.
- **`deny`**: users must track individual pokemon explicitly.
