# `[pvp]`

PVP rank calculation, tracking, and display options.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `level_caps` | array(int) | `[50]` | Level caps used in rank calculations, e.g. `[50, 51]`. |
| `include_mega_evolution` | bool | `false` | Include mega evolutions in rank calculations. |
| `evolution_direct_tracking` | bool | `false` | Allow users to track PVP evolutions directly (e.g. tracking `vaporeon` matches an `eevee`). |
| `filter_by_track` | bool | `false` | Auto-filter PVP listings by the user's track requirements. |
| `filter_max_rank` | int | `10` | Minimum rank cutoff applied by `!track`. |
| `filter_great_min_cp` | int | `1400` | Min CP enforced by `!track` for Great League. |
| `filter_ultra_min_cp` | int | `2350` | Min CP enforced by `!track` for Ultra League. |
| `filter_little_min_cp` | int | `450` | Min CP enforced by `!track` for Little League. |
| `display_max_rank` | int | `10` | Max rank exposed to DTS for filtering calculations. |
| `display_great_min_cp` | int | `1400` | Display CP threshold for Great League. |
| `display_ultra_min_cp` | int | `2350` | Display CP threshold for Ultra League. |
| `display_little_min_cp` | int | `450` | Display CP threshold for Little League. |

The `filter_*` settings are minimums applied to the `!track` command to keep tracks sane and prevent unexpectedly large alert volumes. The `display_*` settings are passed into DTS so templates can perform their own filtering.
