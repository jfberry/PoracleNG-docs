# Gym & Fort Tracking

Track gym team changes and fort (pokestop / gym) updates.

!!! tip "Filter syntax"
    Filters use `key:value` form — `team:instinct`, `d:500`, `area:downtown`. Bare team words (`instinct`, `valor`) still work without the `team:` prefix. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Gym Tracking

Track gyms changing to a specific team:

=== "Discord"

    ```
    !gym <team> [options...]
    ```

=== "Telegram"

    ```
    /gym <team> [options...]
    ```

### Track by Team

=== "Discord"

    ```
    !gym team:instinct                  # Track gyms taken by Instinct
    !gym team:valor                     # Track gyms taken by Valor
    !gym team:mystic                    # Track gyms taken by Mystic
    !gym everything                     # Track all gym team changes
    ```

=== "Telegram"

    ```
    /gym team:instinct
    /gym everything
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`. The bare team word also works without `team:`.

### Track with Distance / Areas

=== "Discord"

    ```
    !gym team:instinct d:500            # Instinct gyms within 500m
    !gym team:valor area:downtown       # Valor takeovers restricted to one area
    ```

=== "Telegram"

    ```
    /gym team:instinct d:500
    ```

### Track Specific Gym by Name

=== "Discord"

    ```
    !gym team:valor gym:Central Park Fountain
    ```

### Track EX-Eligible Gyms

=== "Discord"

    ```
    !gym everything ex                  # Only EX-eligible gyms
    ```

=== "Telegram"

    ```
    /gym everything ex
    ```

## Fort Tracking

Track pokestop and gym updates (name changes, photo updates, new stops/gyms):

=== "Discord"

    ```
    !fort [options...]
    ```

=== "Telegram"

    ```
    /fort [options...]
    ```

!!! note
    Fort tracking must be enabled by the admin (`enable_gym_battle` in tracking config).

## All Gym Options

| Option | Description | Example |
|--------|-------------|---------|
| `team:instinct` / `valor` / `mystic` / `harmony` | Team filter | `team:valor` |
| `ex` | EX-eligible gyms only | `ex` |
| `gym:<name>` | Specific gym by name | `gym:Hilltop Gym` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All team changes | `everything` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`, bare team words like `valor`) still work.

## Removing Tracks

=== "Discord"

    ```
    !gym remove team:instinct
    !gym remove everything
    ```

=== "Telegram"

    ```
    /gym remove team:instinct
    /gym remove everything
    ```
