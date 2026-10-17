# Profiles

Profiles let you save different sets of tracking subscriptions and switch between them. For example, you might have a "home" profile and a "work" profile with different areas and tracking rules.

## How Profiles Work

- Each profile has its own set of areas, location, and tracking subscriptions
- You can create multiple named profiles
- Switching profiles changes which tracking rules are active
- Profile 1 is the default profile — every user starts on it, and it can't be deleted

!!! note "5.3+: profile 0 is gone"
    Older builds created some users (via `!poracle`, role reconciliation, `!channel add`, `!webhook add`) on a hidden "profile 0". From 5.3 new users always start on profile 1, and the upgrade moves existing users and their rules from profile 0 onto profile 1.

## Managing Profiles

### List Your Profiles

=== "Discord"

    ```
    !profile                            # List profiles (plus usage)
    !profile list
    ```

=== "Telegram"

    ```
    /profile
    /profile list
    ```

The active profile is marked with `*`. Each profile's areas, location and auto-switch schedule are shown, followed by the timezone the schedule is evaluated in.

### Create a Profile

=== "Discord"

    ```
    !profile add work                   # Create a profile called "work"
    !profile add holiday home           # Names can contain spaces
    ```

=== "Telegram"

    ```
    /profile add work
    ```

The new profile gets the lowest free number. Creating a profile does **not** switch to it.

### Switch Profile

=== "Discord"

    ```
    !profile 2                          # Switch by number
    !profile work                       # Switch by name
    !profile switch work                # Same thing, explicit
    ```

=== "Telegram"

    ```
    /profile 2
    /profile work
    ```

Switching to a profile that doesn't exist replies "not found" — use `!profile add` to create it first.

### Copy Tracking to Other Profiles

Copy every tracking rule on your **current** profile to other profiles, by name or number:

=== "Discord"

    ```
    !profile copyto work                # Copy current profile's tracking to "work"
    !profile copyto 3                   # ...to profile 3
    !profile copyto work weekend        # ...to several profiles
    !profile copyto all                 # ...to every other profile
    ```

=== "Telegram"

    ```
    /profile copyto work
    /profile copyto 3
    /profile copyto all
    ```

!!! warning
    `copyto` **replaces** the tracking on the target profiles — their existing rules are deleted first. Targets are matched by profile name (any case) or number; if several profiles share a name, all of them are copied to. Unknown targets, and your current profile, are reported back.

!!! note "5.3+"
    Before 5.3 `copyto` matched names only, so `!profile copyto 3` and every pick from the Discord `/profile copyto` autocomplete (which sends the profile number) failed.

### Delete a Profile

=== "Discord"

    ```
    !profile remove 3                   # By number
    !profile delete work                # By name (delete = remove)
    ```

=== "Telegram"

    ```
    /profile remove 3
    ```

Profile 1 can't be deleted.

## Auto-Switching

Profiles can switch automatically based on day of week and time of day. The processor activates the matching profile at each scheduled time and leaves it active until another scheduled time fires.

### Setting Times

`!profile settime` (alias `!profile hours`) sets the schedule on your **current** profile:

=== "Discord"

    ```
    !profile settime mon09:00 tue18:30 weekday09:00 weekend10:00
    !profile settime every08:00          # Switch to this profile at 08:00 every day
    !profile settime mon09:00,fri17:30   # Comma-separated specs also work
    !profile cleartime                   # Remove this profile's schedule
    ```

=== "Telegram"

    ```
    /profile settime mon09:00 weekday09:00 weekend10:00
    /profile settime every08:00
    /profile cleartime
    ```

Times mark **when this profile becomes active**. List as many as you like (separated by spaces or commas) — they replace any existing schedule on this profile. If any spec is invalid, nothing is saved and the reply names the offending spec.

### Time Spec Grammar

```
[day[:]]HH[:MM]                  single time       e.g. mon09:00, mon:9, 8:30
[day[:]]HH[:MM]-HH[:MM][/step]   range + step      e.g. weekday:9-17/2, mon07:30-18:00
```

- The day prefix is optional — without one the time applies every day.
- A range fires at the start time and then every `step` hours (default 1, max 23) up to and including the end time. `weekday:9-17/2` fires at 09:00, 11:00, 13:00, 15:00 and 17:00 Monday–Friday.
- Ranges can't cross midnight (the end must be after the start).

### Day Prefixes

| Prefix | Days |
|---|---|
| `mon` `tue` `wed` `thu` `fri` `sat` `sun` | A single day |
| `weekday` | Mon – Fri |
| `weekend` | Sat – Sun |
| `every` (or `everyday`) | All seven days |

English prefixes are always accepted; translated prefixes for your language work too.

### Timezone

Schedules are evaluated in the timezone of your **location** (`!location`). If you have no location set, the operator's `default_timezone` is used, falling back to the server's local time. `!profile list` shows which timezone applies and the current time there.

!!! tip
    The same time-spec syntax is used by `!summary quest settime` for [scheduled alert summaries](summaries.md#setting-a-delivery-schedule).

## Example: Home and Work Setup

=== "Discord"

    ```
    # Profile 1 is your default — use it for home
    !profile 1
    !location 40.7128,-74.0060
    !area add queens brooklyn
    !track pikachu iv90

    # Create and set up a work profile (profile 2)
    !profile add work
    !profile work
    !location 40.7580,-73.9855
    !area add manhattan
    !track pikachu iv100 d500

    # Switch between them
    !profile 1                          # Use home settings
    !profile 2                          # Use work settings
    ```
