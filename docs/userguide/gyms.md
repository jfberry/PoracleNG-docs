# Gym & Fort Tracking

Track gym team changes and fort updates.

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
    !gym instinct                       # Track gyms taken by Instinct
    !gym valor                          # Track gyms taken by Valor
    !gym mystic                         # Track gyms taken by Mystic
    !gym everything                     # Track all gym team changes
    ```

=== "Telegram"

    ```
    /gym instinct
    /gym everything
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`

### Track with Distance

=== "Discord"

    ```
    !gym instinct d500                  # Instinct gyms within 500m
    ```

=== "Telegram"

    ```
    /gym instinct d500
    ```

### Track Specific Gym by Name

=== "Discord"

    ```
    !gym valor gym:Central Park Fountain
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
| `instinct`/`yellow` | Instinct team | `instinct` |
| `valor`/`red` | Valor team | `valor` |
| `mystic`/`blue` | Mystic team | `mystic` |
| `harmony`/`gray` | No team (gray) | `harmony` |
| `ex` | EX-eligible gyms only | `ex` |
| `d<n>` | Distance in meters | `d500` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `everything` | All team changes | `everything` |
| `remove` | Remove tracking | `remove` |

## Removing Tracks

=== "Discord"

    ```
    !gym remove instinct
    !gym remove everything
    ```

=== "Telegram"

    ```
    /gym remove instinct
    /gym remove everything
    ```
