# Muting Alerts

`!mute` silences alerts for a specific gym, pokemon, area, pokestop, station, or all alerts at once — for a time-bounded window. Unlike `!stop` (which pauses everything) and unlike removing a tracking rule, a mute is **temporary** and **scoped**.

!!! note "Mutes don't survive a restart"
    `!mute` is always available — there is nothing for the operator to switch on. Mutes are held in memory only, so they are lost if Poracle restarts; just re-run the command if that happens.

## Basic Usage

=== "Discord"

    ```
    !mute pokemon pikachu                       # Silence Pikachu alerts for 1 hour (default)
    !mute pokemon pikachu duration:30m          # ...for 30 minutes
    !mute pokemon pikachu duration:2h           # ...for 2 hours
    !mute gym "Hilltop Gym"                     # Silence one gym for 1 hour
    !mute area downtown duration:3h             # Silence everything in this area for 3 hours
    !mute everything duration:1h                # Silence ALL alerts for 1 hour
    ```

=== "Telegram"

    ```
    /mute pokemon pikachu
    /mute pokemon pikachu duration:30m
    /mute gym "Hilltop Gym"
    /mute area downtown duration:3h
    /mute everything duration:1h
    ```

If you omit `duration:`, the mute defaults to 1 hour.

## Scopes

The first argument selects what to silence.

| Scope | Argument | Example |
|---|---|---|
| `everything` | none | `!mute everything duration:2h` |
| `id:<N>` | a tracking rule ID (from `!tracked`) | `!mute id:42` |
| `gym` | gym name or ID | `!mute gym "Hilltop Gym"` |
| `pokemon` | pokemon name or dex ID | `!mute pokemon pikachu` |
| `area` | an area you're allowed to select (as in `!area list`) | `!mute area downtown` |
| `pokestop` | pokestop hex ID | `!mute pokestop abc123def456` |
| `station` | max-battle station ID | `!mute station 789...` |

There is a shorthand for the most common case — if the first argument isn't a known scope word, `!mute` assumes you meant `pokemon`:

```
!mute pikachu              # same as: !mute pokemon pikachu
!mute pikachu duration:2h  # same as: !mute pokemon pikachu duration:2h
```

### Muting One Tracking Rule

`!mute id:<N>` silences the alerts produced by a single tracking rule, leaving your other rules for the same pokemon, gym or area alone. Rule IDs are shown by `!tracked`.

In 5.3+, editing a rule keeps its ID. Re-running a tracking command with new filters (`!track pikachu iv:90` and then `!track pikachu iv:90 d:500`) updates the existing rule in place, so a rule mute, a remembered `id:N`, and the mute and unsubscribe buttons on alerts already sent keep pointing at it. On older builds an edited rule got a new ID, and a rule mute set before the edit stopped working.

=== "Discord"

    ```
    !mute id:42                                 # Silence rule 42 for 1 hour
    !mute id:42 duration:3h
    !mute raid id:17                            # Same thing — the type word is optional
    ```

=== "Telegram"

    ```
    /mute id:42
    /mute raid id:17
    ```

## Duration Syntax

The duration must be given as `duration:<value>`. A bare `2h` is not understood.

| Form | Meaning |
|---|---|
| `90s` | seconds |
| `30m` | minutes |
| `2h` | hours |
| `1h30m` | combined units (any Go-style duration) |
| `7d` | whole days only — `d` can't be combined with other units (`1d12h` is rejected; use `36h`) |

A unit is required: `duration:30` is rejected. If you omit `duration:` entirely, the mute lasts 1 hour.

## Per-type Aliases

Tracking commands (`!track`, `!raid`, `!egg`, `!quest`, `!invasion` / `!incident`, `!lure`, `!nest`, `!gym`, `!fort`, `!maxbattle`) accept `mute` / `unmute` as their first argument. Whatever follows is handled exactly like `!mute` / `!unmute`, so every scope works:

=== "Discord"

    ```
    !raid mute id:17 duration:2h                # same as: !mute id:17 duration:2h
    !track mute id:45                           # same as: !mute id:45
    !raid mute gym "Hilltop Gym"                # same as: !mute gym "Hilltop Gym"
    !invasion mute pokestop abc123def456        # same as: !mute pokestop abc123def456
    !maxbattle mute station 789...              # same as: !mute station 789...
    !raid mute everything duration:1h           # same as: !mute everything duration:1h
    !track mute pikachu                         # same as: !mute pokemon pikachu
    !raid mute mewtwo                           # same as: !mute pokemon mewtwo
    !gym mute "Hilltop Gym"                     # same as: !mute gym "Hilltop Gym"
    !raid unmute gym "Hilltop Gym"              # same as: !unmute gym "Hilltop Gym"
    ```

=== "Telegram"

    ```
    /raid mute id:17 duration:2h
    /track mute id:45
    /raid mute gym "Hilltop Gym"
    /track mute pikachu
    /gym mute "Hilltop Gym"
    ```

The type word doesn't narrow the mute: `!raid mute gym "Hilltop Gym"` silences every alert from that gym, not just raids. With no scope word, `!gym mute <name>` mutes a gym and `!fort mute`, `!track mute` and the other types mute a pokemon (the same shorthand as `!mute <pokemon>`).

!!! note "5.3+"
    Before 5.3 only `!<type> mute id:<N>`, `!track mute <pokemon>` and `!gym mute <gym>` worked. Other scopes after the type word (`!raid mute gym …`, `!invasion mute pokestop …`) were read as a pokemon name, and `!track mute id:N` didn't mute the rule.

## Mute Buttons

Some alerts may also include a **"Mute this gym (1h)"** button (or similar) — clicking it has the same effect as running the matching `!mute` command for you. Buttons must be enabled by the operator; see [Alert Templates (DTS)](../configuration/dtsadvanced.md#interactive-buttons).

Operators can also add an **Unsubscribe** button, which permanently deletes the tracking rule(s) that produced that alert rather than muting them (5.3+ for pokemon and fort alerts).

## Removing a Mute Early

=== "Discord"

    ```
    !unmute pokemon pikachu                     # Remove the pikachu mute
    !unmute gym "Hilltop Gym"                   # Remove one gym mute
    !unmute area downtown                       # Remove an area mute
    !unmute id:42                               # Remove a rule mute
    !unmute everything                          # Clear all of your active mutes
    !unmute all                                 # Same as 'everything'
    ```

=== "Telegram"

    ```
    /unmute pokemon pikachu
    /unmute everything
    ```

Area names are matched without regard to case, so `!unmute area downtown` ends a mute set with `!mute area Downtown` (5.3+; older builds needed the exact display-cased name).

Expired mutes clean themselves up — you only need `!unmute` to end one early.

## Viewing Active Mutes

`!tracked` lists your active mutes alongside your tracking rules, with the time remaining on each.

=== "Discord"

    ```
    !tracked
    ```

=== "Telegram"

    ```
    /tracked
    ```

## How Mute Differs From the Alternatives

| When you want to... | Use |
|---|---|
| Silence one species or gym for a few hours | `!mute` |
| Pause everything indefinitely | `!stop` |
| Stop receiving a species permanently | `!untrack <pokemon>` |
| Stop receiving alerts for a region you no longer care about | `!area remove <area>` |

## Related Commands

- [`!stop` / `!start`](commands.md#registration-status) — indefinite pause across all alerts.
- [`!tracked`](commands.md#registration-status) — review tracking rules and active mutes.
- [Alert Templates (DTS) — Interactive Buttons](../configuration/dtsadvanced.md#interactive-buttons) — operators can attach one-click mute buttons to alerts.
