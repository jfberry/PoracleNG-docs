# PVP Tracking

Track pokemon based on their PVP (Player vs Player) league rankings. PoracleNG calculates PVP ranks for Great League, Ultra League, and Little League.

!!! tip "Filter syntax"
    PVP filters use `key:value` form: `great:5`, `ultra:1-10`, `greatcp:1400`. Bare-minimum-with-colon (`great:5`) means "rank 5 or better"; ranges (`great:1-5`) are preferred when you want explicit bounds. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax) for the full convention.

## How PVP Ranks Work

PVP ranks are calculated based on a pokemon's stat product (ATK × DEF × STA) at the league's CP cap. Rank 1 is the best possible IV spread for that league. **Lower rank numbers are better.**

## Basic PVP Tracking

Track pokemon by their PVP rank in a specific league:

=== "Discord"

    ```
    !track pikachu great:5              # Great League rank 1-5
    !track pikachu ultra:10             # Ultra League rank 1-10
    !track pikachu little:5             # Little League rank 1-5
    ```

=== "Telegram"

    ```
    /track pikachu great:5
    /track pikachu ultra:10
    /track pikachu little:5
    ```

### Track a Range of Ranks

=== "Discord"

    ```
    !track pikachu great:1-5            # Great League ranks 1 through 5
    !track pikachu ultra:1-3            # Ultra League top 3
    ```

=== "Telegram"

    ```
    /track pikachu great:1-5
    ```

### Track with Minimum CP

Ensure the pokemon has a minimum CP for that league:

=== "Discord"

    ```
    !track pikachu great:5 greatcp:1400  # Great League rank 5+, CP >= 1400
    !track pikachu ultra:10 ultracp:2350 # Ultra League rank 10+, CP >= 2350
    ```

=== "Telegram"

    ```
    /track pikachu great:5 greatcp:1400
    ```

### Track Everything by PVP Rank

=== "Discord"

    ```
    !track everything great:1           # All rank 1 Great League pokemon
    !track everything ultra:5           # All top 5 Ultra League pokemon
    ```

=== "Telegram"

    ```
    /track everything great:1
    /track everything ultra:5
    ```

### Combine Multiple Leagues

You can combine multiple leagues in one command (matches any one of them):

=== "Discord"

    ```
    !track pikachu great:1-10 ultra:1-10
    ```

=== "Telegram"

    ```
    /track pikachu great:1-10 ultra:1-10
    ```

### PVP with Level Cap

Specify a PVP level cap for calculations:

=== "Discord"

    ```
    !track pikachu great:5 cap:50       # Great League rank 5 at level cap 50
    !track pikachu great:5 cap:51       # At level cap 51 (best buddy)
    ```

=== "Telegram"

    ```
    /track pikachu great:5 cap:50
    ```

!!! note
    Available level caps depend on your admin's configuration (`[pvp] level_caps`). Use `cap:0` to match any configured cap.

## Combining PVP with Other Filters

PVP tracking can be combined with other pokemon tracking options:

=== "Discord"

    ```
    !track pikachu great:5 d:500        # Great League rank 5, within 500m
    !track pikachu great:5 clean        # With auto-delete
    !track pikachu great:5 template:2   # With specific template
    ```

=== "Telegram"

    ```
    /track pikachu great:5 d:500
    ```

## PVP Options

| Option | Description | Example |
|--------|-------------|---------|
| `great:<n>` or `great:<low>-<high>` | Great League (1500 CP) rank | `great:5`, `great:1-5` |
| `ultra:<n>` or `ultra:<low>-<high>` | Ultra League (2500 CP) rank | `ultra:10`, `ultra:1-10` |
| `little:<n>` or `little:<low>-<high>` | Little League (500 CP) rank | `little:5`, `little:1-5` |
| `greatcp:<n>` | Minimum CP for Great League match | `greatcp:1400` |
| `ultracp:<n>` | Minimum CP for Ultra League match | `ultracp:2350` |
| `littlecp:<n>` | Minimum CP for Little League match | `littlecp:450` |
| `cap:<n>` | PVP level cap | `cap:50`, `cap:51` |

Legacy no-colon forms (`great5`, `greatcp1400`, `cap50`) still work.

## How Alerts Look

When a pokemon matches your PVP tracking, the alert includes:

- PVP rank for the matching league
- CP at the league's cap
- Stat product percentage
- If the pokemon can evolve into something with a good PVP rank, that information is included too (if evolution direct tracking is enabled by your admin)
