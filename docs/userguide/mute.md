# Muting Alerts

`!mute` silences alerts for a specific gym, pokemon, area, pokestop, station, or all alerts at once — for a time-bounded window. Unlike `!stop` (which pauses everything) and unlike removing a tracking rule, a mute is **temporary** and **scoped**.

!!! note "Operator-gated"
    Mutes are stored in memory and require the operator to have enabled the mute feature. If you get *"Mute feature isn't enabled here"* when you run `!mute`, ask your admin to enable it.

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
| `gym` | gym name or ID | `!mute gym "Hilltop Gym"` |
| `pokemon` | pokemon name or dex ID | `!mute pokemon pikachu` |
| `area` | an area you've added with `!area add` | `!mute area downtown` |
| `pokestop` | pokestop hex ID | `!mute pokestop abc123def456` |
| `station` | max-battle station ID | `!mute station 789...` |

There is a shorthand for the most common case — if the first argument isn't a known scope word, `!mute` assumes you meant `pokemon`:

```
!mute pikachu              # same as: !mute pokemon pikachu
!mute pikachu duration:2h  # same as: !mute pokemon pikachu duration:2h
```

## Duration Syntax

`duration:` accepts the usual short forms:

| Suffix | Meaning |
|---|---|
| `s` | seconds — e.g. `duration:90s` |
| `m` | minutes — e.g. `duration:30m` |
| `h` | hours — e.g. `duration:2h` (default unit) |
| `d` | days — e.g. `duration:7d` |

If you omit `duration:` entirely, the mute lasts 1 hour.

## Per-type Aliases

Most tracking commands accept `mute` as a first argument and forward to `!mute` with the type filled in. These are convenience aliases — they behave identically to the full `!mute` form.

=== "Discord"

    ```
    !raid mute gym "Hilltop Gym" duration:2h    # same as: !mute gym "Hilltop Gym" duration:2h
    !gym mute gym "Hilltop Gym"                 # same
    !invasion mute pokestop abc123              # same as: !mute pokestop abc123
    ```

=== "Telegram"

    ```
    /raid mute gym "Hilltop Gym" duration:2h
    /gym mute gym "Hilltop Gym"
    /invasion mute pokestop abc123
    ```

Some alerts may also include a **"Mute this gym (1h)"** button — clicking it has the same effect as running `!mute gym <gym-id> duration:1h` for you. Buttons must be enabled by the operator; see [Alert Templates (DTS)](../configuration/dtsadvanced.md#interactive-buttons).

## Removing a Mute Early

=== "Discord"

    ```
    !unmute pokemon pikachu                     # Remove the pikachu mute
    !unmute gym "Hilltop Gym"                   # Remove one gym mute
    !unmute area downtown                       # Remove an area mute
    !unmute everything                          # Clear all of your active mutes
    !unmute all                                 # Same as 'everything'
    ```

=== "Telegram"

    ```
    /unmute pokemon pikachu
    /unmute everything
    ```

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
