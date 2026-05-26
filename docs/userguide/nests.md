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
| `d:<n>` | Distance in metres | `d:2000` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All nest pokemon | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d2000`) still work.

## Removing Tracks

=== "Discord"

    ```
    !nest remove pikachu
    !nest remove everything
    ```

=== "Telegram"

    ```
    /nest remove pikachu
    /nest remove everything
    ```
