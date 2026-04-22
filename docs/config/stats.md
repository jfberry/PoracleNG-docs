# `[stats]`

Rolling rarity calculation thresholds. These determine which rarity bucket a pokemon falls into based on its share of total sightings inside the rolling window.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `min_sample_size` | int | `10000` | Minimum sightings before rarity is calculated at all. |
| `window_hours` | int | `8` | Rolling window length used for rarity and shiny stats. |
| `refresh_interval_mins` | int | `5` | How often stats are recalculated. |
| `rarity_group_2_uncommon` | float | `1.0` | Max % of total sightings to be considered uncommon. |
| `rarity_group_3_rare` | float | `0.5` | Max % to be considered rare. |
| `rarity_group_4_very_rare` | float | `0.03` | Max % to be considered very rare. |
| `rarity_group_5_ultra_rare` | float | `0.01` | Max % to be considered ultra rare. |

A pokemon below `min_sample_size` is treated as having no rarity data. Rarity groups cascade — anything more common than `rarity_group_2_uncommon` falls into group 1 (common).
