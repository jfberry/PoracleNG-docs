# Gym & Fort Tracking

Track gym team changes and fort (pokestop / gym / station) updates.

!!! tip "Filter syntax"
    Filters use `key:value` form — `d:500`, `area:downtown`, `template:2`. Teams are **bare words** (`instinct`, `valor`, `red`…) — there is no `team:` prefix. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Gym Tracking

Track gyms changing team:

=== "Discord"

    ```
    !gym [team] [options...]
    ```

=== "Telegram"

    ```
    /gym [team] [options...]
    ```

### Track by Team

=== "Discord"

    ```
    !gym instinct                       # Gyms taken by Instinct
    !gym valor                          # Gyms taken by Valor
    !gym blue                           # Gyms taken by Mystic
    !gym harmony                        # Gyms becoming uncontested (gray)
    !gym d:500                          # Any team change within 500m (no team = any team)
    !gym everything                     # One rule per team (all four teams)
    ```

=== "Telegram"

    ```
    /gym instinct
    /gym everything
    ```

Team names: `instinct`/`yellow`, `valor`/`red`, `mystic`/`blue`, `harmony`/`gray`. The team is the gym's **new** team after the change.

### Slot and Battle Changes

By default a gym rule only fires when the gym changes team. Add these keywords (with the underscore) to also be alerted when the team stays the same:

=== "Discord"

    ```
    !gym valor slot_changes             # Also alert when defender slots open up or fill
    !gym everything battle_changes      # Also alert when a gym comes under attack
    !gym mystic slot_changes battle_changes
    ```

=== "Telegram"

    ```
    /gym valor slot_changes
    /gym everything battle_changes
    ```

!!! note
    `battle_changes` requires the admin to set `enable_gym_battle = true` in the `[tracking]` config section; otherwise the bot replies "Battle changes tracking is not enabled".

### Track with Distance / Areas

=== "Discord"

    ```
    !gym instinct d:500                 # Instinct gyms within 500m
    !gym valor area:downtown            # Valor takeovers restricted to one area
    ```

=== "Telegram"

    ```
    /gym instinct d:500
    ```

### Track a Specific Gym

`gym:` pins the rule to one gym, by gym ID or by name. Multi-word names must be kept together — use underscores, or wrap the whole option in double quotes:

=== "Discord"

    ```
    !gym valor gym:central_park_fountain
    !gym everything "gym:Central Park Fountain" slot_changes
    ```

=== "Telegram"

    ```
    /gym valor gym:central_park_fountain
    ```

If the name matches several gyms the bot lists them (up to 10) with their IDs. Admins can restrict this option with the `specificgym` key in `command_security`; without that key everyone can use it.

!!! note
    There is no `ex` option on `!gym` — EX-eligibility is a raid/egg filter (`!raid level:5 ex`).

## All Gym Options

| Option | Description | Example |
|--------|-------------|---------|
| `instinct` / `valor` / `mystic` / `harmony` (or `yellow` / `red` / `blue` / `gray`) | Team filter (new team); omit for any team | `valor` |
| `everything` | One rule for each of the four teams | `everything` |
| `slot_changes` | Also alert on defender slot changes | `slot_changes` |
| `battle_changes` | Also alert when the gym is in battle (needs `enable_gym_battle`) | `battle_changes` |
| `gym:<id\|name>` | Specific gym (underscores or quotes for multi-word names) | `gym:hilltop_gym` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `remove` | Remove tracking | `remove` |

Legacy no-colon forms (`d500`) still work.

## Removing Gym Tracks

=== "Discord"

    ```
    !gym remove instinct                # Remove Instinct rules
    !gym remove id:12                   # One rule by ID (see !tracked)
    !gym remove everything              # Remove all gym tracking
    ```

=== "Telegram"

    ```
    /gym remove instinct
    /gym remove everything
    ```

`!gym remove` with no team also removes **all** your gym rules.

## Fort Tracking

Track pokestop, gym and station edits — new forts, removals, moves, and name / description / photo changes:

=== "Discord"

    ```
    !fort [fort type] [change types...] [options...]
    ```

=== "Telegram"

    ```
    /fort [fort type] [change types...] [options...]
    ```

!!! note
    Fort tracking is on unless the admin disables it with `disable_fort_update = true` in the `[general]` config section.

### Fort Type

Pick at most one; omit it (or use `everything`) for all fort types:

| Keyword | Tracks |
|---------|--------|
| `pokestop` | Pokestops |
| `gym` | Gyms |
| `station` | Power spots / stations |
| `everything` | All of the above (default) |

### Change Types

Add any number; with none, every kind of change alerts:

| Keyword | Alerts when |
|---------|-------------|
| `new` | A fort is added |
| `removal` | A fort is removed |
| `location` | A fort moves |
| `name` | The name changes |
| `description` | The description changes |
| `photo` | The photo changes |

Add `include_empty` to also receive alerts for forts that have no name or description (by default these are skipped).

=== "Discord"

    ```
    !fort pokestop new                  # New pokestops
    !fort gym name photo                # Gym name or photo edits
    !fort everything removal d:1000     # Any fort removed within 1km
    !fort new include_empty area:downtown
    ```

=== "Telegram"

    ```
    /fort pokestop new
    /fort everything removal d:1000
    ```

## All Fort Options

| Option | Description | Example |
|--------|-------------|---------|
| `pokestop` / `gym` / `station` / `everything` | Fort type | `pokestop` |
| `new` / `removal` / `location` / `name` / `description` / `photo` | Change types | `new` |
| `include_empty` | Include forts with no name/description | `include_empty` |
| `d:<n>` | Distance in metres | `d:1000` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `remove` | Remove tracking | `remove` |

`!fort` does not accept `clean` or `edit`.

## Removing Fort Tracks

=== "Discord"

    ```
    !fort remove pokestop               # Remove your pokestop fort rules
    !fort remove id:12                  # One rule by ID (see !tracked)
    !fort remove everything             # Remove all fort tracking
    ```

=== "Telegram"

    ```
    /fort remove pokestop
    /fort remove everything
    ```

`!fort remove` with no fort type removes **all** your fort rules.
