# Invasion & Incident Tracking

Track Team Rocket invasions and other pokestop incidents (Kecleon, Gold Pokéstops, Showcases).

!!! note
    `!incident` (Discord) / `/incident` (Telegram) and `!invasion` / `/invasion` are the same command — both track grunts, leaders and pokestop events.

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
    !incident d:500                     # No type given = everything (within 500m)
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

Use `boss` to track every leader at once (Giovanni, Arlo, Cliff and Sierra, including event variants):

```
!incident boss
```

Other grunt names from the game data (for example `mixed`, or the `npc_*` / `player_team_leader` event grunts) can be typed with underscores; they are matched with or without them.

## Track Events

Non-grunt pokestop events — Gold Pokéstops, Kecleon, Showcases. Gold-Stop and Kecleon alerts use the `incident` template; Showcases use their own `showcase` template (leaderboard and featured pokemon):

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

!!! note "Showcase leaderboard updates"
    A Showcase fires when it starts and again as the top entry changes. If the rule has the edit bit, those updates edit one message in place instead of sending new ones. `!incident` does not accept the `edit` keyword; in 5.3+ the admin can turn edit-in-place on for every incident rule with `default_edit = ["incident"]` in `[tracking]`. Showcase alerts can be switched off entirely with `disable_showcase`.

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
| `boss` | All leaders (Giovanni, Arlo, Cliff, Sierra) | `boss` |
| `<event>` | Event name (`kecleon`, `gold-stop`, `showcase`) | `kecleon` |
| `male` | Male grunts only | `male` |
| `female` | Female grunts only | `female` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All invasion types (also the default when no type is given) | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`) still work. `edit` is not accepted on `!incident` / `!invasion`.

## Removing Tracks

=== "Discord"

    ```
    !incident remove dragon             # Stop tracking dragon grunts
    !incident remove dragon female      # Only the female dragon rule
    !incident remove female             # Every female-only rule
    !incident remove everything         # Remove all invasion tracking
    !incident remove                    # Same as remove everything
    !incident remove id:12              # One rule by ID (see !tracked)
    !untrack incident id:12             # Same as !incident remove id:12
    ```

=== "Telegram"

    ```
    /incident remove dragon
    /incident remove everything
    ```

`!incident remove` (or `!invasion remove`) with nothing after it removes **all** your incident rules. A gender narrows the removal to rules with that gender.

Any word that isn't a known type, leader or event is rejected, on add and on remove alike: `!incident remove dragn` replies "No valid invasion types found" and removes nothing, and `!incident dragon watr` replies "Unrecognized: watr" and saves nothing.

!!! note "5.3+"
    On older builds a typo after `remove` was treated as "everything" and **deleted every incident rule**, a typo next to a valid type was silently dropped, and a gender on remove was ignored.
