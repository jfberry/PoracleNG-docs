# Invasion Tracking

Track Team Rocket invasions by grunt type, leader, or pokemon type.

!!! note
    The command is named `!incident` (Discord) / `/incident` (Telegram) — "incident" is the in-game term for what players call a Team Rocket invasion.

## Basic Usage

=== "Discord"

    ```
    !incident <type> [options...]
    ```

=== "Telegram"

    ```
    /incident <type> [options...]
    ```

## Track by Grunt Type

=== "Discord"

    ```
    !incident dragon                    # Dragon-type grunts
    !incident water                     # Water-type grunts
    !incident fire                      # Fire-type grunts
    !incident everything                # All invasion types
    ```

=== "Telegram"

    ```
    /incident dragon
    /incident everything
    ```

## Track Leaders

=== "Discord"

    ```
    !incident giovanni                  # Giovanni
    !incident cliff                     # Cliff
    !incident sierra                    # Sierra
    !incident arlo                      # Arlo
    ```

=== "Telegram"

    ```
    /incident giovanni
    /incident cliff sierra arlo
    ```

## Track by Gender

=== "Discord"

    ```
    !incident dragon female             # Female dragon grunts
    !incident water male                # Male water grunts
    ```

=== "Telegram"

    ```
    /incident dragon female
    ```

## Track with Distance

=== "Discord"

    ```
    !incident giovanni d500             # Giovanni within 500m
    !incident everything d1000          # All invasions within 1km
    ```

=== "Telegram"

    ```
    /incident giovanni d500
    ```

## All Invasion Options

| Option | Description | Example |
|--------|-------------|---------|
| `<grunt_type>` | Grunt type (dragon, water, fire, etc.) | `dragon` |
| `<leader_name>` | Leader name | `giovanni` |
| `male` | Male grunts only | `male` |
| `female` | Female grunts only | `female` |
| `d<n>` | Distance in meters | `d500` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All invasion types | `everything` |
| `remove` | Remove tracking | `remove` |

## Removing Tracks

=== "Discord"

    ```
    !incident remove dragon             # Stop tracking dragon grunts
    !incident remove everything         # Remove all invasion tracking
    ```

=== "Telegram"

    ```
    /incident remove dragon
    /incident remove everything
    ```
