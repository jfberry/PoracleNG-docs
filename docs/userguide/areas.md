# Areas & Locations

Every alert needs at least one of: an **area** subscription (a geofenced region your admin set up) or a **location** (a coordinate from which distance is measured). Most tracking rules use one or the other; advanced users override on a per-rule basis with `area:` or `location:`.

## Areas

Areas are predefined geographic zones set up by your admin. Subscribing to an area is how you tell Poracle "I want alerts in this region".

### List Available Areas

=== "Discord"

    ```
    !area list
    ```

=== "Telegram"

    ```
    /area list
    ```

Lists every area configured by the admin that you're allowed to use.

### Add an Area

=== "Discord"

    ```
    !area add downtown                  # Add a single area
    !area add downtown uptown midtown   # Add multiple
    ```

=== "Telegram"

    ```
    /area add downtown
    /area add downtown uptown midtown
    ```

### Remove an Area

=== "Discord"

    ```
    !area remove downtown               # Remove one
    !area clear                         # Remove all
    ```

=== "Telegram"

    ```
    /area remove downtown
    /area clear
    ```

### View Your Areas

=== "Discord"

    ```
    !area                               # Show your current areas
    !tracked                            # Areas + every tracking rule + active mutes
    ```

=== "Telegram"

    ```
    /area
    /tracked
    ```

## Location

Your **default location** is a single latitude/longitude used by any tracking rule with a distance filter (`d:500` = within 500 metres of your default location).

### Set Your Default Location

=== "Discord"

    ```
    !location 40.7128,-74.0060          # By coordinates
    !location 10 Downing Street, London # By address (requires the operator to have a geocoder configured)
    ```

=== "Telegram"

    ```
    /location 40.7128,-74.0060
    /location 10 Downing Street, London
    ```

Telegram users can also send a location pin from the mobile app to set their default location.

### Check / Clear

=== "Discord"

    ```
    !location                           # Show your current default
    !location remove default            # Clear the default
    ```

=== "Telegram"

    ```
    /location
    /location remove default
    ```

## Named (Saved) Locations

In addition to your default, you can save **named locations** and refer to them on a per-rule basis. Useful when you want some alerts measured from home, some from work, and some from a holiday rental — without switching profiles.

### Save a Named Location

=== "Discord"

    ```
    !location add Home 51.5074,-0.1278          # By coords
    !location add Home Canterbury               # By address (requires geocoder)
    !location add "Holiday Home" santa monica   # Multi-word names need quotes
    ```

=== "Telegram"

    ```
    /location add Home 51.5074,-0.1278
    /location add Home Canterbury
    /location add "Holiday Home" santa monica
    ```

### List, Show, Remove

=== "Discord"

    ```
    !location list                              # Default + every saved location
    !location show Home                         # One named location's coords + address
    !location show default                      # ...or the default
    !location remove Home                       # Delete (refuses if any tracking rule references it)
    ```

=== "Telegram"

    ```
    /location list
    /location show Home
    /location remove Home
    ```

Poracle refuses to delete a named location while any tracking rule references it — the error message lists the offending rules so you can edit them first.

## Per-Rule Overrides

The `location:` and `area:` keywords let any single tracking rule override your defaults.

Every tracking command (`!track`, `!raid`, `!egg`, `!quest`, `!incident`, `!lure`, `!nest`, `!gym`, `!fort`, `!maxbattle`) can override the default area/location filter on a per-rule basis.

### `location:<name>`

Use a named location as the centre point for this rule's distance check. Requires `d:` on the same rule.

=== "Discord"

    ```
    !track pikachu d:500 location:Home          # Within 500m of "Home" instead of your default
    !raid level:5 d:1000 location:Work          # Level 5 raids within 1km of "Work"
    ```

=== "Telegram"

    ```
    /track pikachu d:500 location:Home
    /raid level:5 d:1000 location:Work
    ```

### `area:<name>[,<name>...]`

Restrict this rule to one or more specific areas, overriding your default `!area add` set.

=== "Discord"

    ```
    !track pikachu area:london,paris            # This rule only matches in London or Paris
    !quest rare_candy area:city_centre          # Rare-candy quests, only in the city centre area
    ```

=== "Telegram"

    ```
    /track pikachu area:london,paris
    /quest rare_candy area:city_centre
    ```

### Mutual-exclusion Rules

The four override params interact with strict rules:

| Combination | OK? |
|---|---|
| `d:` alone (uses your default location) | yes |
| `d:` + `location:<name>` (distance from named location) | yes |
| `area:<name>` alone (restrict to those areas) | yes |
| `d:` + `area:` | **rejected** — distance and area filters are mutually exclusive |
| `area:` + `location:` | **rejected** — one or the other |
| `location:` alone (no `d:`) | **rejected** — `location:` requires `d:` |

### Seeing the Overrides on `!tracked`

`!tracked` shows `@ <label>` and `in <areas>` next to any rule that uses overrides:

```
!track pikachu d:500 location:Home
    → on !tracked:  pikachu  d:500 @ Home

!track pikachu area:london,paris
    → on !tracked:  pikachu  in london,paris
```

## How Areas, Location, and Distance Interact

Without any overrides, the matcher picks one of three modes per rule:

| Rule | Mode |
|---|---|
| Has `d:<n>` → distance from your **default location** |
| Has no `d:` and no `area:` → matches in any area you've added with `!area add` |
| Has `area:<name>` → matches only in that area (ignores your default areas) |
| Has `d:<n> location:<name>` → distance from the **named location** instead of the default |

You can mix modes across rules: have one set of distance-from-home rules, another set restricted to a specific area, all on the same profile.

### Example: Combined Setup

=== "Discord"

    ```
    !area add downtown                          # Default area for area-based rules
    !location 40.7128,-74.0060                  # Default location for distance rules
    !location add Work 40.7580,-73.9855         # Save a second location

    !track pikachu iv:90                         # IV 90%+ pikachu in downtown
    !track pikachu iv:100 d:500                  # Perfect pikachu within 500m of default
    !track gible iv:100 d:1000 location:Work     # Perfect gibles within 1km of Work
    !raid level:5 area:financial_district        # Level 5 raids restricted to one area
    ```

=== "Telegram"

    ```
    /area add downtown
    /location 40.7128,-74.0060
    /location add Work 40.7580,-73.9855

    /track pikachu iv:90
    /track pikachu iv:100 d:500
    /track gible iv:100 d:1000 location:Work
    /raid level:5 area:financial_district
    ```

## REST API

Named locations and per-rule overrides are also available over HTTP — see the [`API.md`](https://github.com/jfberry/PoracleNG/blob/main/API.md) document in the PoracleNG repo for the full endpoint surface (`GET /api/humans/{id}/locations`, `POST /api/humans/{id}/locations/add`, `POST /api/humans/{id}/locations/{label}/delete`).
