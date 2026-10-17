# `[pvp]`

PVP rank tracking and display options. PVP ranks come from Golbat's webhooks; PoracleNG does not calculate them itself.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `level_caps` | array(int) | `[50]` | Level caps to accept, e.g. `[50, 51]`. **Must match the caps configured in Golbat's PVP settings.** Ranks Golbat sends for a cap not listed here are ignored, and a warning is logged once per cap. With `[50]`, a cap-51 (best buddy) rank never matches PVP tracking. |
| `include_mega_evolution` | bool | `false` | Default for PVP rules that don't use a `mega` keyword. `false`: those rules match base forms only. `true`: they also match mega/temporary-evolution ranks. A rule's own `mega` / `mega:x` / `mega:y` keyword always overrides this. |
| `evolution_direct_tracking` | bool | `false` | Allow users to track PVP evolutions directly (e.g. tracking `vaporeon` matches an `eevee`). |
| `filter_by_track` | bool | `false` | Auto-filter PVP listings in alerts by the user's track requirements. |
| `filter_max_rank` | int | `100` | **Worst** (highest-numbered) rank a user may track. A `!track` request for a worse rank is clamped to this. (`config.example.toml` ships `10`.) |
| `filter_great_min_cp` | int | `1400` | CP floor applied by `!track` to Great League rules. |
| `filter_ultra_min_cp` | int | `2350` | CP floor applied by `!track` to Ultra League rules. |
| `filter_little_min_cp` | int | `450` | CP floor applied by `!track` to Little League rules. |
| `pvp_query_max_rank` | int | `100` | Worst rank considered when matching webhook PVP data against tracking rules. If set to `0`, falls back to `filter_max_rank`. |
| `display_max_rank` | int | `10` | Max rank exposed to DTS for filtering calculations. |
| `display_great_min_cp` | int | `1400` | Display CP threshold for Great League. |
| `display_ultra_min_cp` | int | `2350` | Display CP threshold for Ultra League. |
| `display_little_min_cp` | int | `450` | Display CP threshold for Little League. |
| `force_min_cp` | bool | `false` | Reported to web front ends (PoracleWeb) through the config API as `pvpRequiresMinCp`, so they require a minimum CP on PVP filters. Has no effect on `!track`. |
| `data_source` | string | `"webhook"` | **Deprecated.** `webhook` is the only supported source; `ohbem` is not implemented. |

The `filter_*` settings limit what `!track` accepts, so PVP tracking stays reasonable and doesn't produce unexpectedly large alert volumes. The `display_*` settings are passed into DTS so templates can do their own filtering.

See also: [PVP tracking](../userguide/pvp.md).
