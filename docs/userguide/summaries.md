# Alert Summaries

Alert summaries let you receive **grouped, scheduled** messages instead of one alert per event. Mark a tracking rule with the `summary` keyword and matched events go into a per-user buffer; the `!summary` command controls when that buffer is flushed and how it's grouped.

!!! note "Currently quest-only"
    Today only `!quest` rules can be flagged for summary delivery. The `!summary` command takes an alert type — `quest` is the only valid value right now. Future releases may extend summary delivery to other alert types; the syntax in this page is written so it'll continue to work as more alert types are added.

## Opting a Rule In

Add `summary` to any `!quest` rule when you create it:

=== "Discord"

    ```
    !quest pikachu summary              # Pikachu encounter quests, buffered
    !quest rare candy summary           # Rare Candy quests, buffered
    !quest energy summary               # All mega energy quests, buffered
    ```

=== "Telegram"

    ```
    /quest pikachu summary
    /quest rare candy summary
    ```

`!tracked` displays `summary` next to each opted-in rule. See [Quest Tracking](quests.md#buffered-summary-delivery) for more on how grouped quest messages look.

To stop using summary delivery for a rule, recreate the rule without `summary`, or remove and re-add it.

## Setting a Delivery Schedule

`!summary <alert-type> settime <times>` sets when the buffered events fire. Times are wall-clock; the buffer is flushed at each time, grouped by reward, and dispatched. Listing new times replaces the entire previous schedule (it does not merge).

=== "Discord"

    ```
    !summary quest settime mon07:30 weekday07:30 weekend10:00
    !summary quest settime every08:00            # Once a day, 08:00, every day
    !summary quest settime mon9 wed18 fri20      # Colon and minutes optional → mon09:00, wed18:00, fri20:00
    ```

=== "Telegram"

    ```
    /summary quest settime mon07:30 weekday07:30 weekend10:00
    /summary quest settime every08:00
    ```

Specs can be separated by spaces or commas (`mon07:30,weekend10:00`). If any spec is invalid nothing is saved, and the reply names the spec that failed.

### Time Spec Grammar

```
[day[:]]HH[:MM]                  single time       e.g. mon07:30, weekday:7, 8:30
[day[:]]HH[:MM]-HH[:MM][/step]   range + step      e.g. weekday:9-17/2, every08:00-20:00/4
```

- The day prefix is optional — without one the time applies every day.
- A range fires at the start time and then every `step` hours (default 1, max 23) up to and including the end time. `weekday:9-17/2` flushes at 09:00, 11:00, 13:00, 15:00 and 17:00 Monday–Friday.
- Ranges can't cross midnight (the end must be after the start).

=== "Discord"

    ```
    !summary quest settime weekday:9-17/2 weekend10:00
    ```

=== "Telegram"

    ```
    /summary quest settime weekday:9-17/2 weekend10:00
    ```

### Day Prefixes

| Prefix | Days |
|---|---|
| `mon` `tue` `wed` `thu` `fri` `sat` `sun` | A single day |
| `weekday` | Mon – Fri |
| `weekend` | Sat – Sun |
| `every` (or `everyday`) | All seven days |

List as many times as you like in one command; together they describe the full week's schedule.

### Timezone

Times are evaluated in the timezone of your **location** (`!location`). With no location set, the operator's `default_timezone` is used, falling back to the server's local time. `!summary quest` shows which timezone applies and the current time there.

## Checking Your Schedule

`!summary <alert-type>` with no further argument shows your current schedule and how many events are sitting in the buffer right now.

=== "Discord"

    ```
    !summary quest
    ```

=== "Telegram"

    ```
    /summary quest
    ```

On Discord slash commands this is `/summary quest show`.

## Clearing the Schedule

=== "Discord"

    ```
    !summary quest cleartime
    ```

=== "Telegram"

    ```
    /summary quest cleartime
    ```

Removes all delivery times. New matches still buffer — they just won't auto-dispatch. You can still flush them manually with `!summary quest now`.

## Flushing the Buffer Now

`!summary <alert-type> now` immediately dispatches everything currently buffered for that alert type, regardless of schedule. The schedule itself is unchanged.

=== "Discord"

    ```
    !summary quest now
    ```

=== "Telegram"

    ```
    /summary quest now
    ```

Useful when you want to see what's queued without waiting for the next scheduled time.

## How Grouping Works

When the buffer fires, entries are grouped by reward — every pokestop offering the same reward becomes a single message with a multi-pin map. For example, ten different pokestops handing out Rare Candy across the morning will arrive as one "Rare Candy ×10" message at 07:30 rather than ten separate alerts.

If a reward group is too big to fit in a single Discord embed, it's split across consecutive messages — each chunk gets its own map showing only that chunk's pokestops, and the header says e.g. `(1/3)`.

## Restart Safety

The buffer is snapshotted to disk when the bot shuts down and reloaded on startup, so you won't lose queued events across a normal restart. Entries that have expired (e.g. quests that have already despawned) are dropped from the buffer automatically.

## Tips

- Combine `summary` with normal filters: `!quest rare candy summary d2000` buffers Rare Candy quests within 2 km of you.
- A digest at the start of your play session works well — `!summary quest settime weekday17:30 weekend09:00` gives you Monday–Friday after work and weekend mornings in one go.
- Use `!summary quest now` once after `settime` to confirm what your schedule will dispatch.

## Related Commands

- [`!quest`](quests.md) — quest tracking, including the `summary` keyword.
- [`!profile settime`](profiles.md#auto-switching) — profile auto-switching uses the same time-spec syntax.
