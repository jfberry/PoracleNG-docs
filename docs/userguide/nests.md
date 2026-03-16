# Nest Tracking

Track nest pokemon changes in your areas.

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
    !nest pikachu d2000                 # Pikachu nests within 2km
    ```

=== "Telegram"

    ```
    /nest pikachu
    /nest everything
    /nest pikachu d2000
    ```

## All Nest Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Pokemon name or ID | `pikachu`, `25` |
| `d<n>` | Distance in meters | `d2000` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All nest pokemon | `everything` |
| `remove` | Remove tracking | `remove` |

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
