# Nest Tracking

Track nest pokemon changes in your areas.

!!! tip "Filter syntax"
    Filters use `key:value` form — `d:2000`, `area:downtown`, `template:2`. Legacy `d2000` is still accepted. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Basic Usage

=== "Discord"

    ```
    !nest <pokemon> [options...]
    ```

=== "Telegram"

    ```
    /nest <pokemon> [options...]
    ```

## Examples

=== "Discord"

    ```
    !nest pikachu                       # Track Pikachu nests
    !nest gible                         # Track Gible nests
    !nest everything                    # Track all nest changes
    !nest pikachu d:2000                # Pikachu nests within 2km
    !nest gible area:downtown           # Gible nests restricted to an area
    !nest pikachu minspawn:10           # Only nests averaging 10+ spawns per hour
    !nest everything minspawn:20        # Any nest averaging 20+ spawns per hour
    !nest vulpix form:alola             # Alolan Vulpix nests only
    ```

=== "Telegram"

    ```
    /nest pikachu
    /nest everything
    /nest pikachu d:2000
    ```

## All Nest Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Pokemon name or ID | `pikachu`, `25` |
| `minspawn:<n>` | Minimum average spawns per hour for the nest | `minspawn:10` |
| `form:<name>` | Form filter (named pokemon only) | `form:alola` |
| `gen:<n>` | Narrow `everything`/named pokemon to a generation | `gen:4` |
| `<type>` | Narrow `everything`/named pokemon to a type | `dragon` |
| `d:<n>` | Distance in metres | `d:2000` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All nest pokemon | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d2000`, `minspawn10`) still work.

!!! note "Type and generation"
    Type names and `gen:` **narrow `everything` or a list of pokemon you have named** (handy with the `+` evolution suffix, e.g. `!nest bulbasaur+ charmander+ fire`). On their own (`!nest dragon`) they reply "no pokemon". With `everything` (`!nest everything gen:1`, `!nest everything fire`) they work as on `!track`: one rule is stored for every matching species rather than a single catch-all rule (5.3+; older builds ignored the filter and tracked every nest).

## Removing Tracks

=== "Discord"

    ```
    !nest remove pikachu                # All Pikachu nest rules (every form)
    !nest remove id:12                  # One rule by ID (see !tracked)
    !nest remove everything
    ```

=== "Telegram"

    ```
    /nest remove pikachu
    /nest remove everything
    ```

`form:` is not accepted on remove — removal is species-wide. Remove a single form's rule by its ID.
