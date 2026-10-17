# MAX Battle Tracking

Track MAX (Dynamax / Gigantamax) Battles.

!!! tip "Filter syntax"
    Filters use `key:value` form — `level:5`, `d:500`, `area:downtown`. Legacy `level5` / `d500` still work. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Basic Usage

Track by level:

=== "Discord"

    ```
    !maxbattle level:5
    !maxbattle level:1-3
    ```

=== "Telegram"

    ```
    /maxbattle level:5
    ```

| Level | Battle |
|-------|--------|
| 1–4 | 1–4 star Max Battle |
| 5 | Legendary Max Battle |
| 7 | Gigantamax Battle |
| 8 | Legendary Gigantamax Battle |

Track all MAX Battle levels:

```
!maxbattle everything
```

`everything` creates a single "all levels" rule (stored as level 90; `!tracked` shows it as all levels).

Track a specific pokemon:

```
!maxbattle charizard
!maxbattle snorlax
```

Pokemon rules match the pokemon at any battle level.

## Gigantamax Filter

To only receive alerts for Gigantamax variants, add `gmax`:

```
!maxbattle level:5 gmax
!maxbattle charizard gmax
```

## Filtering Options

### By Distance / Areas

```
!maxbattle level:5 d:500
!maxbattle everything d:1000
!maxbattle level:5 d:1000 location:Work
!maxbattle level:5 area:downtown
```

### By Form

```
!maxbattle toxtricity form:low_key
```

An unknown form name is rejected with an error. Use `!info <pokemon> forms` to see valid names.

### By Move

```
!maxbattle everything move:flamethrower
!maxbattle charizard move:blast_burn
```

### Type and Generation

Type names and `gen:` only **narrow a list of pokemon you have named** — they are not standalone filters:

```
!maxbattle charmander+ squirtle+ fire    # Only the fire-type members (Charmander line)
!maxbattle bulbasaur+ machop+ gen:1      # Both families are gen 1, so both are kept
```

`!maxbattle fire` or `!maxbattle gen:1` on their own reply "No levels specified", and combining them with `everything` or `level:` (`!maxbattle everything fire`) is silently ignored — you get every MAX Battle.

## All MAX Battle Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Specific pokemon (any level); `+` suffix adds evolutions | `charizard` |
| `level:<n>` / `level:<n>-<m>` | MAX Battle level or range | `level:5` |
| `everything` | All levels (one level-90 rule) | `everything` |
| `gmax` | Gigantamax variant only | `gmax` |
| `form:<name>` | Form filter (named pokemon only) | `form:low_key` |
| `move:<name>` | Filter by move | `move:flamethrower` |
| `gen:<n>` | Narrow named pokemon to a generation | `gen:1` |
| `<type>` | Narrow named pokemon to a type | `fire` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete when the MAX Battle expires | `clean` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`level5`, `d500`, `gen1`) still work. `edit` is not accepted on `!maxbattle`.

## Removing Tracking

Remove tracking for a pokemon, a single rule, or everything:

```
!maxbattle remove charizard          # All Charizard rules (every form)
!maxbattle remove id:12              # One rule by ID (see !tracked)
!maxbattle remove everything         # All MAX Battle tracking
!untrack maxbattle id:12             # Same as !maxbattle remove id:12
```

!!! note
    Level rules can only be removed by ID or with `everything` — `!maxbattle remove level:5` currently matches nothing. `form:` is not accepted on remove (removal is species-wide); remove a single form's rule by its ID.

## Examples

```
!maxbattle level:5                     # All level 5 MAX Battles
!maxbattle everything d:500            # All levels within 500m
!maxbattle charizard gmax              # Gigantamax Charizard only
!maxbattle level:7 d:1000 clean        # Gigantamax battles within 1km, auto-delete
```
