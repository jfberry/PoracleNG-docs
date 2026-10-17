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

### Best-rank bound

`great:5` means ranks 1–5. To set only the *best* rank you want (for example to skip the very top ranks you already have), use `greathigh:` / `ultrahigh:` / `littlehigh:`:

=== "Discord"

    ```
    !track pikachu greathigh:3 great:20  # Great League ranks 3 through 20
    ```

=== "Telegram"

    ```
    /track pikachu greathigh:3 great:20
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
    Available level caps depend on your admin's configuration (`[pvp] level_caps`). Use `cap:0` to match any configured cap. Asking for a cap that isn't configured is rejected with the list of valid caps.

## Mega Evolution Ranks

PVP ranks can be calculated for a pokemon's mega evolution as well as its base form. Add `mega` to a PVP rule to match on the mega's rank:

=== "Discord"

    ```
    !track charmander+ great:10 mega     # Rank as any mega evolution (Mega X or Mega Y)
    !track charizard ultra:5 mega:x      # Rank as Mega Charizard X only
    !track charizard ultra:5 mega:y      # Rank as Mega Charizard Y only
    ```

=== "Telegram"

    ```
    /track charmander+ great:10 mega
    /track charizard ultra:5 mega:x
    ```

| Rule | Matches |
|------|---------|
| no `mega` | Base-form ranks only — unless your admin has turned on `[pvp] include_mega_evolution`, in which case mega ranks match too |
| `mega` | Mega / temporary-evolution ranks only (any variant) |
| `mega:x` / `mega:y` | Only that specific mega variant |

The `mega` keywords always override the server's `include_mega_evolution` default.

!!! note
    `mega` only has an effect together with a league filter (`great:`, `ultra:`, `little:` …); on a non-PVP rule it is ignored. If you ask for `mega:x` / `mega:y` on a species that has no such mega form, the rule is still saved but the reply warns that it can never match.

## Server Limits

Your admin's `[pvp]` settings bound what you can track:

- **Worst rank** — the rank you ask for is capped at `filter_max_rank` (10 in the example config). With that setting `great:100` is stored as `great:10`.
- **Minimum CP** — league rules are given a minimum CP of at least `filter_great_min_cp` / `filter_ultra_min_cp` / `filter_little_min_cp` (1400 / 2350 / 450 in the example config), even if you set a lower `greatcp:` etc.
- **Permission** — PVP tracking can be restricted to certain users or roles by your admin.

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
| `greathigh:<n>`, `ultrahigh:<n>`, `littlehigh:<n>` | Best rank to accept | `greathigh:3` |
| `greatcp:<n>` | Minimum CP for Great League match | `greatcp:1400` |
| `ultracp:<n>` | Minimum CP for Ultra League match | `ultracp:2350` |
| `littlecp:<n>` | Minimum CP for Little League match | `littlecp:450` |
| `cap:<n>` | PVP level cap | `cap:50`, `cap:51` |
| `mega` | Match mega-evolution ranks (any variant) | `mega` |
| `mega:x`, `mega:y` | Match a specific mega variant's ranks | `mega:x` |

Legacy no-colon forms (`great5`, `greatcp1400`, `cap50`) still work.

## How Alerts Look

When a pokemon matches your PVP tracking, the alert includes:

- PVP rank for the matching league
- CP at the league's cap
- Stat product percentage
- If the pokemon can evolve into something with a good PVP rank, that information is included too (if evolution direct tracking is enabled by your admin)
