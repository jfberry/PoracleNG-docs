# Lure Tracking

Track lure modules placed on pokestops.

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
    !lure mossy magnetic                # Track mossy and magnetic lures
    !lure everything                    # Track all lure types
    !lure glacial d500                  # Glacial lures within 500m
    ```

=== "Telegram"

    ```
    /lure glacial
    /lure everything
    /lure glacial d500
    ```

## All Lure Options

| Option | Description | Example |
|--------|-------------|---------|
| `<lure_type>` | Lure type name | `glacial` |
| `d<n>` | Distance in meters | `d500` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All lure types | `everything` |
| `remove` | Remove tracking | `remove` |

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
