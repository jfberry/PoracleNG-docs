# Pokemon Tracking

Track wild pokemon spawns with customizable filters for IVs, level, CP, PVP rank, rarity, and more.

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
    !track pikachu iv90          # IV 90% or higher
    !track pikachu iv90-100      # IV between 90-100%
    !track gible iv0             # IV 0% (nundo)
    ```

=== "Telegram"

    ```
    /track pikachu iv90
    /track pikachu iv90-100
    /track gible iv0
    ```

### Track by Level and CP

=== "Discord"

    ```
    !track pikachu level35              # Level 35+
    !track pikachu level30-35           # Level 30 to 35
    !track pikachu cp2000               # CP 2000+
    !track pikachu cp2000-3000          # CP between 2000-3000
    ```

=== "Telegram"

    ```
    /track pikachu level35
    /track pikachu level30-35
    /track pikachu cp2000
    ```

### Track by Individual Stats

=== "Discord"

    ```
    !track pikachu atk15                # Attack 15
    !track pikachu atk15 def15 sta15    # Perfect IVs (specific stats)
    !track pikachu atk0 def0 sta0       # Nundo (all stats 0)
    ```

=== "Telegram"

    ```
    /track pikachu atk15
    /track pikachu atk15 def15 sta15
    ```

### Track by Distance

=== "Discord"

    ```
    !track pikachu iv100 d500           # Within 500m of your location
    !track pikachu iv90 d2000           # Within 2km
    ```

=== "Telegram"

    ```
    /track pikachu iv100 d500
    /track pikachu iv90 d2000
    ```

!!! note
    Distance tracking requires you to have set a location with `!location`.

### Track Everything

=== "Discord"

    ```
    !track everything iv100             # All pokemon with 100% IV
    !track everything iv0               # All nundos
    !track everything level35           # All level 35+
    ```

=== "Telegram"

    ```
    /track everything iv100
    /track everything iv0
    /track everything level35
    ```

!!! warning
    `!track everything` without filters is not allowed for non-admin users to prevent alert floods.

### Track by Type

=== "Discord"

    ```
    !track dragon iv90                  # All dragon-type pokemon with 90%+ IV
    !track water fire iv100             # All water and fire types with 100% IV
    ```

=== "Telegram"

    ```
    /track dragon iv90
    /track water fire iv100
    ```

### Track by Generation

=== "Discord"

    ```
    !track everything gen1 iv100        # All gen 1 pokemon with 100% IV
    !track everything gen4 iv90         # All gen 4 with 90%+
    ```

=== "Telegram"

    ```
    /track everything gen1 iv100
    ```

### Track by Form

=== "Discord"

    ```
    !track pikachu form:libre           # Specific form
    !track castform form:sunny
    ```

=== "Telegram"

    ```
    /track pikachu form:libre
    ```

### Track by Rarity

=== "Discord"

    ```
    !track everything rarity4           # Very rare pokemon
    !track everything rarity5           # Ultra rare pokemon
    !track everything rarity3-5         # Rare to ultra rare
    ```

=== "Telegram"

    ```
    /track everything rarity4
    ```

Rarity levels: 1 (Common), 2 (Uncommon), 3 (Rare), 4 (Very Rare), 5 (Ultra Rare)

### Track by Size

=== "Discord"

    ```
    !track pikachu size1                # Tiny size
    !track pikachu size5                # XXL size
    ```

=== "Telegram"

    ```
    /track pikachu size1
    ```

### Track by Weight

=== "Discord"

    ```
    !track pikachu weight0-1            # Lightweight pikachu
    ```

=== "Telegram"

    ```
    /track pikachu weight0-1
    ```

### Track Evolutions

Append `+` to a pokemon name to include its entire evolution line:

=== "Discord"

    ```
    !track eevee+ iv100                 # Eevee and all eeveelutions
    !track bulbasaur+ iv90              # Bulbasaur, Ivysaur, Venusaur
    ```

=== "Telegram"

    ```
    /track eevee+ iv100
    ```

### Track with Gender Filter

=== "Discord"

    ```
    !track pikachu female iv100         # Female only
    !track pikachu male iv90            # Male only
    ```

=== "Telegram"

    ```
    /track pikachu female iv100
    ```

### Track PVP Ranks

See [PVP Tracking](pvp.md) for detailed PVP tracking examples.

=== "Discord"

    ```
    !track pikachu great5               # Great League rank 5 or better
    !track pikachu ultra10              # Ultra League rank 10 or better
    !track pikachu little5              # Little League rank 5 or better
    ```

=== "Telegram"

    ```
    /track pikachu great5
    ```

## All Options

| Option | Description | Example |
|--------|-------------|---------|
| `iv<n>` | Minimum IV percentage | `iv90`, `iv90-100` |
| `maxiv<n>` | Maximum IV percentage | `maxiv50` |
| `level<n>` | Minimum pokemon level | `level35`, `level30-35` |
| `maxlevel<n>` | Maximum pokemon level | `maxlevel30` |
| `cp<n>` | Minimum CP | `cp2000`, `cp2000-3000` |
| `maxcp<n>` | Maximum CP | `maxcp1500` |
| `atk<n>` | Minimum attack IV (0-15) | `atk15`, `atk14-15` |
| `maxatk<n>` | Maximum attack IV | `maxatk10` |
| `def<n>` | Minimum defense IV (0-15) | `def15` |
| `maxdef<n>` | Maximum defense IV | `maxdef10` |
| `sta<n>` | Minimum stamina IV (0-15) | `sta15` |
| `maxsta<n>` | Maximum stamina IV | `maxsta10` |
| `d<n>` | Distance in meters from location | `d500`, `d2000` |
| `t<n>` | Minimum time remaining (seconds) | `t300` |
| `form:<name>` | Specific form name | `form:alolan` |
| `gen<n>` | Pokemon generation | `gen1`, `gen4` |
| `rarity<n>` | Minimum rarity (1-5) | `rarity3`, `rarity3-5` |
| `maxrarity<n>` | Maximum rarity | `maxrarity4` |
| `size<n>` | Minimum size (1-5) | `size1`, `size1-3` |
| `maxsize<n>` | Maximum size | `maxsize3` |
| `weight<n>` | Minimum weight | `weight0-1` |
| `maxweight<n>` | Maximum weight | `maxweight100` |
| `great<n>` | Great League max rank | `great5` |
| `ultra<n>` | Ultra League max rank | `ultra10` |
| `little<n>` | Little League max rank | `little5` |
| `cap<n>` | PVP level cap | `cap50`, `cap51` |
| `male` | Male pokemon only | `male` |
| `female` | Female pokemon only | `female` |
| `genderless` | Genderless pokemon only | `genderless` |
| `template<n>` | Alert template number | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All pokemon | `everything` |
| `individually` | Track as individual rows | `individually` |

## Removing Tracks

=== "Discord"

    ```
    !untrack pikachu                    # Stop tracking pikachu
    !untrack everything                 # Remove all pokemon tracking
    ```

=== "Telegram"

    ```
    /untrack pikachu
    /untrack everything
    ```

## Viewing Your Tracks

=== "Discord"

    ```
    !tracked                            # Show all tracking subscriptions
    ```

=== "Telegram"

    ```
    /tracked
    ```
