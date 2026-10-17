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
| `normal` | Normal (standard) Lure Module |
| `glacial` | Glacial Lure |
| `mossy` | Mossy Lure |
| `magnetic` | Magnetic Lure |
| `rainy` | Rainy Lure |
| `sparkly` | Sparkly Lure |
| `everything` | All lure types |

Naming several types creates **one rule per type**, so each shows (and can be removed) separately in `!tracked`. `everything` is a single "any lure" rule.

## Examples

=== "Discord"

    ```
    !lure glacial                       # Track glacial lures
    !lure mossy magnetic                # Track mossy and magnetic (two rules)
    !lure normal glacial edit           # Normal + glacial, updates edit in place
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
| `edit` | Repeat alerts for the same lure edit the original message instead of sending a new one | `edit` |
| `everything` | All lure types | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`) still work.

## Removing Tracks

=== "Discord"

    ```
    !lure remove glacial                # Stop tracking glacial lures
    !lure remove mossy magnetic         # Remove both rules
    !lure remove everything             # Remove all lure tracking
    !lure remove id:12                  # One rule by ID (see !tracked)
    !untrack lure id:12                 # Same as !lure remove id:12
    ```

=== "Telegram"

    ```
    /lure remove glacial
    /lure remove everything
    /lure remove id:12
    ```

!!! note "5.3+"
    Removing a lure rule by ID (`!lure remove id:N`, `!untrack lure id:N`, or picking a rule in the Discord `/untrack lure` menu) only works from 5.3. Older builds replied "No lure type specified", so remove by lure type instead.
