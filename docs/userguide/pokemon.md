# Pokemon Tracking

Track wild pokemon spawns with customizable filters for IVs, level, CP, PVP rank, rarity, and more.

## Filter Syntax

All filters use `key:value` form — e.g. `iv:90`, `cp:2000-3000`, `d:500`. **Ranges** are written `low-high` (e.g. `iv:90-100`, `level:25-35`) and are the preferred way to set both a minimum and a maximum.

Bare minimum-with-colon (`iv:99` to mean "at least 99% IV") is fine and common — the range form is just what you reach for when you also want an upper bound. Always prefer `iv:90-100` over `miniv:90 maxiv:100`; the range reads better and is easier to edit.

!!! note "Legacy forms still work"
    Older syntax (`iv90`, `cp2000`, `d500`, `maxiv50`) is still accepted, but documentation and examples use the colon/range form throughout. If you're scripting, prefer the colon form going forward.

## Basic Usage

=== "Discord"

    ```
    !track <pokemon_name> [options...]
    ```

=== "Telegram"

    ```
    /track <pokemon_name> [options...]
    ```

## Examples

### Track a Specific Pokemon

=== "Discord"

    ```
    !track pikachu
    !track 25
    ```

=== "Telegram"

    ```
    /track pikachu
    /track 25
    ```

You can use either the pokemon name or its Pokedex number.

### Track with IV Filter

=== "Discord"

    ```
    !track pikachu iv:90          # IV 90% or higher
    !track pikachu iv:90-100      # IV between 90% and 100%
    !track gible iv:0             # IV 0% (nundo)
    ```

=== "Telegram"

    ```
    /track pikachu iv:90
    /track pikachu iv:90-100
    /track gible iv:0
    ```

### Track by Level and CP

=== "Discord"

    ```
    !track pikachu level:35              # Level 35+
    !track pikachu level:30-35           # Level 30 to 35
    !track pikachu cp:2000               # CP 2000+
    !track pikachu cp:2000-3000          # CP between 2000-3000
    ```

=== "Telegram"

    ```
    /track pikachu level:35
    /track pikachu level:30-35
    /track pikachu cp:2000
    ```

### Track by Individual Stats

=== "Discord"

    ```
    !track pikachu atk:15                       # Attack 15
    !track pikachu atk:15 def:15 sta:15         # Perfect IVs (specific stats)
    !track pikachu atk:0 def:0 sta:0            # Nundo (all stats 0)
    !track pikachu atk:13-15 def:13-15 sta:13-15 # Near-perfect ranges
    ```

=== "Telegram"

    ```
    /track pikachu atk:15
    /track pikachu atk:15 def:15 sta:15
    ```

### Track by Distance

=== "Discord"

    ```
    !track pikachu iv:100 d:500          # Within 500m of your default location
    !track pikachu iv:90 d:2000          # Within 2km
    !track pikachu iv:100 d:500 location:Work  # 500m of your named "Work" location
    ```

=== "Telegram"

    ```
    /track pikachu iv:100 d:500
    /track pikachu iv:90 d:2000
    ```

!!! note
    Distance tracking requires a location to be set with `!location`. See [Areas & Locations](areas.md) for default + named locations and per-rule overrides.

### Track in Specific Areas

=== "Discord"

    ```
    !track pikachu iv:100 area:downtown          # Restricted to one area
    !track pikachu iv:100 area:downtown,uptown   # Or several
    ```

=== "Telegram"

    ```
    /track pikachu iv:100 area:downtown,uptown
    ```

`area:` and `d:` are mutually exclusive — see [Per-rule overrides](areas.md#per-rule-overrides).

### Track Everything

=== "Discord"

    ```
    !track everything iv:100             # All pokemon with 100% IV
    !track everything iv:0               # All nundos
    !track everything level:35           # All level 35+
    ```

=== "Telegram"

    ```
    /track everything iv:100
    /track everything iv:0
    /track everything level:35
    ```

!!! warning
    `!track everything` without filters is not allowed for non-admin users to prevent alert floods.

### Track by Type

=== "Discord"

    ```
    !track dragon iv:90                  # All dragon-type pokemon with 90%+ IV
    !track water fire iv:100             # All water and fire types with 100% IV
    ```

=== "Telegram"

    ```
    /track dragon iv:90
    /track water fire iv:100
    ```

### Track by Generation

=== "Discord"

    ```
    !track everything gen:1 iv:100       # All gen 1 pokemon with 100% IV
    !track everything gen:4 iv:90        # All gen 4 with 90%+
    ```

=== "Telegram"

    ```
    /track everything gen:1 iv:100
    ```

### Track by Form

=== "Discord"

    ```
    !track pikachu form:libre            # Specific form
    !track castform form:sunny
    !track vulpix form:alolan
    ```

=== "Telegram"

    ```
    /track pikachu form:libre
    ```

### Track by Rarity

=== "Discord"

    ```
    !track everything rarity:4           # Very rare pokemon
    !track everything rarity:5           # Ultra rare pokemon
    !track everything rarity:3-5         # Rare to ultra rare
    ```

=== "Telegram"

    ```
    /track everything rarity:4
    ```

Rarity levels: 1 (Common), 2 (Uncommon), 3 (Rare), 4 (Very Rare), 5 (Ultra Rare). Use `!info rarity` to see which species are in each tier.

### Track by Size

=== "Discord"

    ```
    !track pikachu size:1                # Tiny size
    !track pikachu size:5                # XXL size
    ```

=== "Telegram"

    ```
    /track pikachu size:1
    ```

### Track by Weight

=== "Discord"

    ```
    !track pikachu weight:0-1            # Lightweight pikachu
    ```

=== "Telegram"

    ```
    /track pikachu weight:0-1
    ```

### Track Evolutions

Append `+` to a pokemon name to include its entire evolution line:

=== "Discord"

    ```
    !track eevee+ iv:100                 # Eevee and all eeveelutions
    !track bulbasaur+ iv:90              # Bulbasaur, Ivysaur, Venusaur
    ```

=== "Telegram"

    ```
    /track eevee+ iv:100
    ```

### Track with Gender Filter

=== "Discord"

    ```
    !track pikachu female iv:100         # Female only
    !track pikachu male iv:90            # Male only
    ```

=== "Telegram"

    ```
    /track pikachu female iv:100
    ```

### Track PVP Ranks

See [PVP Tracking](pvp.md) for the full PVP surface.

=== "Discord"

    ```
    !track pikachu great:1-5             # Great League rank 1-5
    !track pikachu ultra:1-10            # Ultra League rank 1-10
    !track pikachu little:1-5            # Little League rank 1-5
    ```

=== "Telegram"

    ```
    /track pikachu great:1-5
    ```

## All Options

| Option | Description | Example |
|--------|-------------|---------|
| `iv:<n>` | Minimum IV percentage | `iv:90` |
| `iv:<low>-<high>` | IV range (preferred over min/max) | `iv:90-100` |
| `cp:<n>` | Minimum CP | `cp:2000` |
| `cp:<low>-<high>` | CP range | `cp:2000-3000` |
| `level:<n>` | Minimum pokemon level | `level:35` |
| `level:<low>-<high>` | Pokemon level range | `level:30-35` |
| `atk:<n>`, `def:<n>`, `sta:<n>` | Minimum individual IV (0-15) | `atk:15` |
| `atk:<low>-<high>` etc. | Individual IV range | `atk:13-15` |
| `d:<n>` | Distance in metres from your location | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `t:<n>` | Minimum time remaining (seconds) | `t:300` |
| `form:<name>` | Specific form name | `form:alolan` |
| `gen:<n>` | Pokemon generation | `gen:1` |
| `rarity:<n>` or `rarity:<low>-<high>` | Rarity tier (1-5) | `rarity:3-5` |
| `size:<n>` or `size:<low>-<high>` | Size category (1-5) | `size:5` |
| `weight:<low>-<high>` | Weight range | `weight:0-1` |
| `great:<n>`, `ultra:<n>`, `little:<n>` | PVP league rank (single or range) | `great:1-10` |
| `greatcp:<n>`, `ultracp:<n>`, `littlecp:<n>` | Minimum league CP | `greatcp:1400` |
| `cap:<n>` | PVP level cap | `cap:50` |
| `male` / `female` / `genderless` | Gender filter | `male` |
| `template:<n>` | Alert template (see `!info templates`) | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `edit` | Single message, edited in place | `edit` |
| `everything` | All pokemon | `everything` |
| `individually` | Track as individual rows | `individually` |

Legacy no-colon forms (`iv90`, `cp2000`, `d500`, `maxiv50`) and the explicit `maxiv:N`/`maxcp:N`/etc. forms are still accepted, but the colon and range forms are recommended.

## Removing Tracks

=== "Discord"

    ```
    !untrack pikachu                     # Stop tracking pikachu
    !untrack everything                  # Remove all pokemon tracking
    !untrack id:45                       # Remove one specific rule by UID (see !tracked)
    ```

=== "Telegram"

    ```
    /untrack pikachu
    /untrack everything
    /untrack id:45
    ```

## Viewing Your Tracks

=== "Discord"

    ```
    !tracked
    ```

=== "Telegram"

    ```
    /tracked
    ```
