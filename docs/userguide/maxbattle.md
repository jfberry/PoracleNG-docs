# MAX Battle Tracking

Track MAX (Dynamax / Gigantamax) Battles.

!!! tip "Filter syntax"
    Filters use `key:value` form — `level:5`, `d:500`, `area:downtown`. Legacy `level5` / `d500` still work. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Basic Usage

Track by level:

=== "Discord"

    ```
    !maxbattle level:5
    !maxbattle level:3
    ```

=== "Telegram"

    ```
    /maxbattle level:5
    ```

Track all MAX Battle levels:

```
!maxbattle everything
```

Track a specific pokemon:

```
!maxbattle charizard
!maxbattle snorlax
```

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

### By Type

```
!maxbattle fire
!maxbattle dragon
```

### By Generation

```
!maxbattle gen:1
!maxbattle gen:3 level:5
```

### By Form

```
!maxbattle charizard form:mega
```

### By Move

```
!maxbattle everything move:flamethrower
```

Combine move and type:

```
!maxbattle everything move:hidden-power/dragon
```

## All MAX Battle Options

| Option | Description | Example |
|--------|-------------|---------|
| `level:<n>` | MAX Battle level (1-5) | `level:5` |
| `gmax` | Gigantamax variant only | `gmax` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `move:<name>` | Filter by move | `move:flamethrower` |
| `gen:<n>` | Generation filter | `gen:1` |
| `form:<name>` | Form filter | `form:mega` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete when the MAX Battle expires | `clean` |

Legacy no-colon forms (`level5`, `d500`, `gen1`) still work.

## Removing Tracking

Remove tracking for a specific level or pokemon:

```
!maxbattle remove level:5
!maxbattle remove charizard
```

Remove all MAX Battle tracking:

```
!maxbattle remove everything
```

## Examples

```
!maxbattle level:5                     # All level 5 MAX Battles
!maxbattle everything d:500            # All levels within 500m
!maxbattle charizard gmax              # Gigantamax Charizard only
!maxbattle fire level:5 d:1000         # Level 5 fire types within 1km
!maxbattle gen:1 everything clean      # All gen 1 MAX Battles, auto-delete
```
