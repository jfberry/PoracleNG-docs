# Invasion & Incident Tracking

Track Team Rocket invasions and other pokestop incidents (Kecleon, Gold Pokéstops, Showcases).

!!! note
    The command is named `!incident` (Discord) / `/incident` (Telegram) — "incident" is the in-game term. `!invasion` is still accepted as an alias for Team Rocket grunts specifically.

!!! tip "Filter syntax"
    Filters use `key:value` form — `d:500`, `area:downtown`, `template:2`. Legacy `d500` is still accepted. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

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
    !incident giovanni
    !incident cliff
    !incident sierra
    !incident arlo
    ```

=== "Telegram"

    ```
    /incident giovanni
    /incident cliff sierra arlo
    ```

## Track Events

Non-grunt incidents — Gold Pokéstops, Kecleon, Showcases — fire the `incident` template:

=== "Discord"

    ```
    !incident kecleon                   # Kecleon appearances
    !incident gold-stop                 # Gold Pokéstops
    !incident showcase                  # Pokemon Showcases
    ```

=== "Telegram"

    ```
    /incident kecleon
    /incident gold-stop
    /incident showcase
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

## Track with Distance / Areas

=== "Discord"

    ```
    !incident giovanni d:500            # Giovanni within 500m
    !incident everything d:1000         # All invasions within 1km
    !incident kecleon area:downtown     # Kecleon restricted to one area
    ```

=== "Telegram"

    ```
    /incident giovanni d:500
    /incident kecleon area:downtown
    ```

## All Invasion / Incident Options

| Option | Description | Example |
|--------|-------------|---------|
| `<grunt_type>` | Grunt type (dragon, water, fire, etc.) | `dragon` |
| `<leader_name>` | Leader name | `giovanni` |
| `<event>` | Event name (`kecleon`, `gold-stop`, `showcase`) | `kecleon` |
| `male` | Male grunts only | `male` |
| `female` | Female grunts only | `female` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All invasion types | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`) still work.

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
