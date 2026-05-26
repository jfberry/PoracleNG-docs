# Lure Tracking

Track lure modules placed on pokestops.

!!! tip "Filter syntax"
    Filters use `key:value` form — `d:500`, `area:downtown`, `template:2`. Legacy `d500` is still accepted. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Basic Usage

=== "Discord"

    ```
    !lure <type> [options...]
    ```

=== "Telegram"

    ```
    /lure <type> [options...]
    ```

## Lure Types

| Type | Name |
|------|------|
| `normal` | Normal Lure |
| `glacial` | Glacial Lure |
| `mossy` | Mossy Lure |
| `magnetic` | Magnetic Lure |
| `rainy` | Rainy Lure |
| `sparkly` | Sparkly Lure |
| `everything` | All lure types |

## Examples

=== "Discord"

    ```
    !lure glacial                       # Track glacial lures
    !lure mossy magnetic                # Track mossy and magnetic
    !lure everything                    # Track all lure types
    !lure glacial d:500                 # Glacial lures within 500m
    !lure mossy area:downtown           # Mossy lures restricted to an area
    ```

=== "Telegram"

    ```
    /lure glacial
    /lure everything
    /lure glacial d:500
    ```

## All Lure Options

| Option | Description | Example |
|--------|-------------|---------|
| `<lure_type>` | Lure type name | `glacial` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All lure types | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`) still work.

## Removing Tracks

=== "Discord"

    ```
    !lure remove glacial                # Stop tracking glacial lures
    !lure remove everything             # Remove all lure tracking
    ```

=== "Telegram"

    ```
    /lure remove glacial
    /lure remove everything
    ```
