# Raids & Eggs

Track raid bosses and raid eggs by level, pokemon, team, and more.

!!! tip "Filter syntax"
    Filters use `key:value` form — e.g. `level:5`, `d:1000`, `team:valor`. Legacy `level5` / `d1000` / bare team words (`valor`) are still accepted. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax) for the full convention.

## Raid Tracking

Track specific raid bosses:

=== "Discord"

    ```
    !raid mewtwo                        # Track Mewtwo raids
    !raid articuno zapdos moltres       # Multiple bosses, OR-matched
    !raid level:5                       # All level 5 raids
    ```

=== "Telegram"

    ```
    /raid mewtwo
    /raid articuno zapdos moltres
    /raid level:5
    ```

### Track by Level

=== "Discord"

    ```
    !raid level:1                       # Tier 1 raids
    !raid level:3                       # Tier 3 raids
    !raid level:5                       # Tier 5 (legendary) raids
    !raid legendary                     # Alias for level:5
    !raid mega                          # Alias for level:6 (mega raids)
    !raid elite                         # Elite raids
    !raid shadow                        # Shadow raids (level:15)
    !raid ultra beast                   # Level:8 ultra beast raids
    !raid primal                        # Level:10 primal raids
    !raid everything                    # All raid levels
    ```

=== "Telegram"

    ```
    /raid level:5
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
    !raid mewtwo team:instinct          # Mewtwo at yellow gyms
    !raid level:5 team:valor            # Level 5 at red gyms
    !raid level:5 team:mystic           # Level 5 at blue gyms
    !raid level:5 team:harmony          # Level 5 at uncontested (gray) gyms
    ```

=== "Telegram"

    ```
    /raid mewtwo team:instinct
    /raid level:5 team:valor
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`. The bare team word (e.g. `valor`) still works as a filter without `team:`.

### Track EX-Eligible Raids

=== "Discord"

    ```
    !raid level:5 ex                    # Level 5 raids at EX-eligible gyms only
    ```

=== "Telegram"

    ```
    /raid level:5 ex
    ```

### Track a Specific Gym

=== "Discord"

    ```
    !raid level:5 gym:Central Park Fountain
    ```

### Track by Move

=== "Discord"

    ```
    !raid mewtwo move:psystrike         # Mewtwo with Psystrike
    !raid mewtwo move:ice_beam          # Underscores for multi-word moves
    ```

=== "Telegram"

    ```
    /raid mewtwo move:psystrike
    ```

### Track with Distance / Areas

=== "Discord"

    ```
    !raid level:5 d:1000                # Level 5 within 1km of your default location
    !raid level:5 d:1500 location:Work  # 1.5km of your "Work" named location
    !raid level:5 area:financial        # Level 5 restricted to one area
    ```

=== "Telegram"

    ```
    /raid level:5 d:1000
    /raid level:5 area:financial
    ```

See [Areas & Locations](areas.md#per-rule-overrides) for `location:` and `area:` rules.

### RSVP Updates

=== "Discord"

    ```
    !raid level:5 rsvp                  # Include RSVP updates in alerts
    !raid level:5 rsvp_only             # Only RSVP changes, not the initial spawn
    ```

## Egg Tracking

Track raid eggs before they hatch:

=== "Discord"

    ```
    !egg level:5                        # Level 5 eggs
    !egg level:1 level:3                # Level 1 and 3 eggs
    !egg everything                     # All egg levels
    ```

=== "Telegram"

    ```
    /egg level:5
    /egg everything
    ```

### Egg Options

Eggs support the same team, distance, area, EX, and template options as raids:

=== "Discord"

    ```
    !egg level:5 ex                     # Level 5 eggs at EX gyms
    !egg level:5 team:instinct d:500    # Level 5 eggs at yellow gyms within 500m
    ```

=== "Telegram"

    ```
    /egg level:5 ex
    ```

## All Raid/Egg Options

| Option | Description | Example |
|--------|-------------|---------|
| `level:<n>` | Raid tier level | `level:5` |
| `legendary` / `mega` / `elite` / `shadow` / `ultra beast` / `primal` | Level aliases | `legendary` |
| `ex` | EX-eligible gyms only | `ex` |
| `d:<n>` | Distance in metres | `d:1000` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `team:instinct` / `valor` / `mystic` / `harmony` | Gym team filter | `team:valor` |
| `gym:<name>` | Specific gym by name | `gym:Hilltop Gym` |
| `move:<name>` | Specific move (raids only) | `move:psystrike` |
| `rsvp` | Include RSVP updates | `rsvp` |
| `rsvp_only` | Only RSVP change updates | `rsvp_only` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete when raid ends | `clean` |
| `edit` | Single message, edited in place | `edit` |
| `everything` | All levels | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`level5`, `d1000`, bare team names like `valor`) still work.

## Removing Tracks

=== "Discord"

    ```
    !raid remove mewtwo                 # Stop tracking Mewtwo raids
    !raid remove level:5                # Stop tracking level 5 raids
    !raid remove everything             # Remove all raid tracking
    !egg remove level:5                 # Stop tracking level 5 eggs
    !egg remove everything              # Remove all egg tracking
    !untrack id:12                      # Remove by UID (see !tracked)
    ```

=== "Telegram"

    ```
    /raid remove mewtwo
    /raid remove everything
    /egg remove everything
    ```
