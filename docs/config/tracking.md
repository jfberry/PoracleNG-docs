# `[tracking]`

Restrictions and defaults for what users can track.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `everything_flag_permissions` | string | `"allow-any"` | How the `everything` flag is handled by `!track`. See below. |
| `default_distance` | int | `0` | Default tracking radius for distance-only tracking with no areas. |
| `max_distance` | int | `0` | Max tracking circle radius. `0` = no limit. |
| `enable_gym_battle` | bool | `false` | Allow the `battle_changes` option in `!gym`. |
| `default_user_tracking_level_cap` | int | `0` | Default PVP tracking level cap. `0` = all levels. |

## `everything_flag_permissions`

- **`allow-any`** — unrestricted; recorded as a wildcard. Users may also opt to use individual rows.
- **`allow-and-always-individually`** — unrestricted, but always recorded as one row per pokemon.
- **`allow-and-ignore-individually`** — unrestricted, but users cannot opt for individual rows.
- **`deny`** — users must track individual pokemon explicitly.
