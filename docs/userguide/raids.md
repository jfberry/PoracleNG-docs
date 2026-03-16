# Raids & Eggs

Track raid bosses and raid eggs by level, pokemon, team, and more.

## Raid Tracking

Track specific raid bosses:

=== "Discord"

    ```
    !raid mewtwo                        # Track Mewtwo raids
    !raid articuno zapdos moltres       # Track multiple bosses
    !raid level5                        # All level 5 raids
    ```

=== "Telegram"

    ```
    /raid mewtwo
    /raid articuno zapdos moltres
    /raid level5
    ```

### Track by Level

=== "Discord"

    ```
    !raid level1                        # Tier 1 raids
    !raid level3                        # Tier 3 raids
    !raid level5                        # Tier 5 (legendary) raids
    !raid everything                    # All raid levels
    ```

=== "Telegram"

    ```
    /raid level5
    /raid everything
    ```

### Track by Type

=== "Discord"

    ```
    !raid dragon                        # All dragon-type raid bosses
    !raid water fire                    # Water and fire type bosses
    ```

=== "Telegram"

    ```
    /raid dragon
    ```

### Track by Team

Filter to raids at gyms controlled by a specific team:

=== "Discord"

    ```
    !raid mewtwo instinct               # Mewtwo raids at yellow gyms
    !raid level5 valor                  # Level 5 at red gyms
    !raid level5 mystic                 # Level 5 at blue gyms
    !raid level5 harmony                # Level 5 at gray (no team) gyms
    ```

=== "Telegram"

    ```
    /raid mewtwo instinct
    /raid level5 valor
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`

### Track EX-Eligible Raids

=== "Discord"

    ```
    !raid level5 ex                     # Level 5 raids at EX-eligible gyms
    ```

=== "Telegram"

    ```
    /raid level5 ex
    ```

### Track by Move

=== "Discord"

    ```
    !raid mewtwo move:psystrike         # Mewtwo with Psystrike
    !raid mewtwo move:ice beam/ice      # Move with type filter
    ```

=== "Telegram"

    ```
    /raid mewtwo move:psystrike
    ```

### Track with Distance

=== "Discord"

    ```
    !raid level5 d1000                  # Level 5 raids within 1km
    ```

=== "Telegram"

    ```
    /raid level5 d1000
    ```

## Egg Tracking

Track raid eggs before they hatch:

=== "Discord"

    ```
    !egg level5                         # Level 5 eggs
    !egg level1 level3                  # Level 1 and 3 eggs
    !egg everything                     # All egg levels
    ```

=== "Telegram"

    ```
    /egg level5
    /egg everything
    ```

### Egg Options

Eggs support the same team, distance, EX, and template options as raids:

=== "Discord"

    ```
    !egg level5 ex                      # Level 5 eggs at EX-eligible gyms
    !egg level5 instinct d500           # Level 5 eggs at yellow gyms within 500m
    ```

=== "Telegram"

    ```
    /egg level5 ex
    ```

## All Raid/Egg Options

| Option | Description | Example |
|--------|-------------|---------|
| `level<n>` | Raid tier level | `level5` |
| `ex` | EX-eligible gyms only | `ex` |
| `d<n>` | Distance in meters | `d1000` |
| `instinct`/`yellow` | Instinct gyms | `instinct` |
| `valor`/`red` | Valor gyms | `valor` |
| `mystic`/`blue` | Mystic gyms | `mystic` |
| `harmony`/`gray` | Uncontested gyms | `harmony` |
| `move:<name>` | Specific move (raids) | `move:psystrike` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All levels | `everything` |
| `remove` | Remove tracking | `remove` |

## Removing Tracks

=== "Discord"

    ```
    !raid remove mewtwo                 # Stop tracking Mewtwo raids
    !raid remove level5                 # Stop tracking level 5 raids
    !raid remove everything             # Remove all raid tracking
    !egg remove level5                  # Stop tracking level 5 eggs
    !egg remove everything              # Remove all egg tracking
    ```

=== "Telegram"

    ```
    /raid remove mewtwo
    /raid remove everything
    /egg remove everything
    ```
