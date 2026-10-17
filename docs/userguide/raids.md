# Raids & Eggs

Track raid bosses and raid eggs by level, pokemon, team, and more.

!!! tip "Filter syntax"
    Filters use `key:value` form — e.g. `level:5`, `d:1000`, `move:psystrike`. Legacy `level5` / `d1000` are still accepted. Teams are **bare words** (`valor`, `red`) — there is no `team:` prefix. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax) for the full convention.

!!! note "Multi-word keywords"
    The bot splits commands on spaces, so keywords that contain a space must be typed with underscores (or wrapped in double quotes): `mega_legendary`, `shadow_legendary`, `ultra_beast`, `rsvp_only`, `no_rsvp`. Typing `mega legendary` with a space is read as two separate words.

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
    !raid level:1-5                     # Tiers 1 to 5 (one rule per level)
    !raid legendary                     # Level 5
    !raid mega                          # Level 6 (mega raids)
    !raid mega_legendary                # Level 7
    !raid ultra_beast                   # Level 8
    !raid elite                         # Level 9
    !raid primal                        # Level 10
    !raid shadow                        # All shadow levels (11-15)
    !raid shadow_legendary              # Level 15 only
    !raid everything                    # All raid levels
    ```

=== "Telegram"

    ```
    /raid level:5
    /raid shadow
    /raid everything
    ```

Only **one** level filter is read per command — `!raid legendary mega` or `!raid level:1 level:3` rejects the second word. Use a range (`level:1-5`), separate commands, or the `|` separator to add several in one message:

```
!raid legendary | mega | shadow_legendary
```

| Level | Name keyword |
|-------|--------------|
| 1–4 | `level:1` … `level:4` |
| 5 | `legendary` |
| 6 | `mega` |
| 7 | `mega_legendary` |
| 8 | `ultra_beast` |
| 9 | `elite` |
| 10 | `primal` |
| 11–14 | `level:11` … `level:14` (shadow 1–4 star) |
| 15 | `shadow_legendary` |
| 11–15 | `shadow` (all shadow levels) |

!!! warning "No type or generation tracking for raids"
    Raid rules are either *by boss* or *by level*. Pokemon type names (`dragon`, `fire`) and `gen:` are not raid filters: `!raid dragon` replies "No raid levels or pokemon specified", and adding them to a level rule (`!raid everything dragon`) is silently ignored. Name the bosses you want instead.

### Track by Form or Costume

`form:` narrows a named boss to one form; `costume:` narrows by costume (name or ID, `costume:0` = no costume):

=== "Discord"

    ```
    !raid giratina form:origin          # Origin Forme Giratina only
    !raid pikachu costume:0             # Un-costumed Pikachu only
    !raid level:1 costume:5             # Level 1 raids with costume ID 5
    ```

=== "Telegram"

    ```
    /raid giratina form:origin
    /raid pikachu costume:0
    ```

An unknown form or costume name is rejected with an error rather than silently tracking every form. Use `!info <pokemon> forms` / `!info <pokemon> costumes` to see valid names.

### Track by Team

Filter to raids at gyms controlled by a specific team. Teams are bare words:

=== "Discord"

    ```
    !raid mewtwo instinct               # Mewtwo at yellow gyms
    !raid level:5 valor                 # Level 5 at red gyms
    !raid level:5 blue                  # Level 5 at blue (Mystic) gyms
    !raid level:5 harmony               # Level 5 at uncontested (gray) gyms
    ```

=== "Telegram"

    ```
    /raid mewtwo instinct
    /raid level:5 valor
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`.

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

`gym:` pins the rule to one gym, by gym ID or by name. Multi-word names must be kept together — use underscores, or wrap the whole option in double quotes:

=== "Discord"

    ```
    !raid level:5 gym:central_park_fountain
    !raid level:5 "gym:Central Park Fountain"
    ```

=== "Telegram"

    ```
    /raid level:5 gym:central_park_fountain
    ```

If the name matches several gyms the bot lists them (up to 10) with their IDs so you can re-run with the exact ID. Gym lookup needs the scanner database to be configured.

### Track by Move

=== "Discord"

    ```
    !raid mewtwo move:psystrike         # Mewtwo with Psystrike
    !raid mewtwo move:ice_beam          # Underscores (or "ice beam") for multi-word moves
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
    !raid level:5 rsvp                  # First alert plus an update whenever RSVPs change
    !raid level:5 rsvp_only             # Only alert while the raid has upcoming RSVPs
    !raid level:5 no_rsvp               # First alert only (the default)
    ```

=== "Telegram"

    ```
    /raid level:5 rsvp
    /raid level:5 rsvp_only
    ```

## Egg Tracking

Track raid eggs before they hatch:

=== "Discord"

    ```
    !egg level:5                        # Level 5 eggs
    !egg level:1-3                      # Level 1, 2 and 3 eggs
    !egg level:1 | level:3              # Level 1 and level 3 eggs
    !egg shadow                         # All shadow egg levels
    !egg everything                     # All egg levels
    ```

=== "Telegram"

    ```
    /egg level:5
    /egg everything
    ```

### Egg Options

Eggs support the same level names, team, gym, distance, area, EX, RSVP, `clean`/`edit` and template options as raids (but not pokemon, `form:`, `costume:` or `move:`):

=== "Discord"

    ```
    !egg level:5 ex                     # Level 5 eggs at EX gyms
    !egg level:5 instinct d:500         # Level 5 eggs at yellow gyms within 500m
    ```

=== "Telegram"

    ```
    /egg level:5 ex
    ```

## All Raid/Egg Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Raid boss (raids only); `+` suffix adds evolutions | `mewtwo` |
| `level:<n>` / `level:<n>-<m>` | Raid tier level or range | `level:5` |
| `legendary` / `mega` / `mega_legendary` / `ultra_beast` / `elite` / `primal` / `shadow` / `shadow_legendary` | Level names (see table above) | `legendary` |
| `form:<name>` | Boss form (raids only) | `form:origin` |
| `costume:<name\|id>` | Boss costume; `costume:0` = no costume (raids only) | `costume:0` |
| `move:<name>` | Specific move (raids only) | `move:psystrike` |
| `instinct` / `valor` / `mystic` / `harmony` (or `yellow` / `red` / `blue` / `gray`) | Gym team filter | `valor` |
| `ex` | EX-eligible gyms only | `ex` |
| `gym:<id\|name>` | Specific gym (underscores or quotes for multi-word names) | `gym:hilltop_gym` |
| `d:<n>` | Distance in metres | `d:1000` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `rsvp` | First alert plus RSVP change updates | `rsvp` |
| `rsvp_only` | Only alert while RSVPs exist | `rsvp_only` |
| `no_rsvp` | First alert only (default) | `no_rsvp` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete when raid ends | `clean` |
| `edit` | Single message, edited in place as the raid updates | `edit` |
| `everything` | All levels | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`level5`, `d1000`) still work.

## Removing Tracks

=== "Discord"

    ```
    !raid remove mewtwo                 # Stop tracking Mewtwo raids (all forms)
    !raid remove pikachu costume:0      # Only the un-costumed Pikachu rule
    !raid remove level:5                # Stop tracking level 5 raids
    !raid remove everything             # Remove all raid tracking
    !raid remove id:12                  # Remove one raid rule by ID (see !tracked)
    !egg remove level:5                 # Stop tracking level 5 eggs
    !egg remove everything              # Remove all egg tracking
    !untrack raid id:12                 # Same as !raid remove id:12
    ```

=== "Telegram"

    ```
    /raid remove mewtwo
    /raid remove everything
    /egg remove everything
    ```

`form:` is **not** accepted on remove — removal is species-wide, so `!raid remove giratina form:origin` is rejected rather than deleting every Giratina rule. To remove a single form's rule, use its ID from `!tracked` (`!raid remove id:12`).

!!! note
    Plain `!untrack id:12` removes a **pokemon** rule. For raid and egg rules use `!raid remove id:12` / `!egg remove id:12`, or `!untrack raid id:12` / `!untrack egg id:12`.
