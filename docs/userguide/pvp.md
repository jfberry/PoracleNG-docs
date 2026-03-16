# PVP Tracking

Track pokemon based on their PVP (Player vs Player) league rankings. PoracleNG calculates PVP ranks for Great League, Ultra League, and Little League.

## How PVP Ranks Work

PVP ranks are calculated based on a pokemon's stat product (ATK x DEF x STA) at the league's CP cap. Rank 1 is the best possible IV spread for that league. Lower rank numbers are better.

## Basic PVP Tracking

Track pokemon by their PVP rank in a specific league:

=== "Discord"

    ```
    !track pikachu great5               # Great League rank 1-5
    !track pikachu ultra10              # Ultra League rank 1-10
    !track pikachu little5              # Little League rank 1-5
    ```

=== "Telegram"

    ```
    /track pikachu great5
    /track pikachu ultra10
    /track pikachu little5
    ```

### Track a Range of Ranks

=== "Discord"

    ```
    !track pikachu great1-5             # Great League ranks 1 through 5
    !track pikachu ultra1-3             # Ultra League top 3
    ```

=== "Telegram"

    ```
    /track pikachu great1-5
    ```

### Track with Minimum CP

Ensure the pokemon has a minimum CP for that league:

=== "Discord"

    ```
    !track pikachu great5 greatcp1400   # Great League rank 5+, CP >= 1400
    !track pikachu ultra10 ultracp2350  # Ultra League rank 10+, CP >= 2350
    ```

=== "Telegram"

    ```
    /track pikachu great5 greatcp1400
    ```

### Track Everything by PVP Rank

=== "Discord"

    ```
    !track everything great1            # All rank 1 Great League pokemon
    !track everything ultra5            # All top 5 Ultra League pokemon
    ```

=== "Telegram"

    ```
    /track everything great1
    /track everything ultra5
    ```

### PVP with Level Cap

Specify a PVP level cap for calculations:

=== "Discord"

    ```
    !track pikachu great5 cap50         # Great League rank 5 at level cap 50
    !track pikachu great5 cap51         # At level cap 51 (best buddy)
    ```

=== "Telegram"

    ```
    /track pikachu great5 cap50
    ```

!!! note
    Available level caps depend on your admin's configuration (`[pvp] level_caps`). Use `cap0` to match any configured cap.

## Combining PVP with Other Filters

PVP tracking can be combined with other pokemon tracking options:

=== "Discord"

    ```
    !track pikachu great5 d500          # Great League rank 5, within 500m
    !track pikachu great5 clean         # With auto-delete
    !track pikachu great5 template2     # With specific template
    ```

=== "Telegram"

    ```
    /track pikachu great5 d500
    ```

!!! warning
    You cannot combine multiple PVP leagues in the same tracking command. Track them separately:

    ```
    !track pikachu great5
    !track pikachu ultra10
    ```

## PVP Options

| Option | Description | Example |
|--------|-------------|---------|
| `great<n>` | Great League (1500 CP) max rank | `great5`, `great1-5` |
| `ultra<n>` | Ultra League (2500 CP) max rank | `ultra10` |
| `little<n>` | Little League (500 CP) max rank | `little5` |
| `greatcp<n>` | Minimum CP for Great League match | `greatcp1400` |
| `ultracp<n>` | Minimum CP for Ultra League match | `ultracp2350` |
| `littlecp<n>` | Minimum CP for Little League match | `littlecp450` |
| `cap<n>` | PVP level cap | `cap50`, `cap51` |

## How Alerts Look

When a pokemon matches your PVP tracking, the alert will include:

- PVP rank for the matching league
- CP at the league's cap
- Stat product percentage
- If the pokemon can evolve into something with a good PVP rank, that information is included too (if evolution direct tracking is enabled by your admin)
