# Areas & Location

You need either areas or a location (or both) set up to receive alerts. Areas are geofenced regions; location is a specific coordinate for distance-based tracking.

## Areas

Areas are predefined geographic zones set up by your admin. You subscribe to areas to receive alerts for pokemon/raids/etc. within them.

### List Available Areas

=== "Discord"

    ```
    !area list
    ```

=== "Telegram"

    ```
    /area list
    ```

### Add an Area

=== "Discord"

    ```
    !area add downtown                  # Add a single area
    !area add downtown uptown midtown   # Add multiple areas
    ```

=== "Telegram"

    ```
    /area add downtown
    /area add downtown uptown midtown
    ```

### Remove an Area

=== "Discord"

    ```
    !area remove downtown               # Remove a single area
    !area remove everything             # Remove all areas
    ```

=== "Telegram"

    ```
    /area remove downtown
    /area remove everything
    ```

### View Your Areas

=== "Discord"

    ```
    !tracked                            # Shows areas along with tracking subscriptions
    ```

=== "Telegram"

    ```
    /tracked
    ```

## Location

Your location is a specific latitude/longitude coordinate used for distance-based tracking (e.g. `d500` = within 500 meters).

### Set Your Location

=== "Discord"

    ```
    !location 40.7128,-74.0060         # Set by coordinates
    !location New York City            # Set by place name (requires geocoding)
    ```

=== "Telegram"

    ```
    /location 40.7128,-74.0060
    /location New York City
    ```

You can also send a Telegram location pin to set your location.

### Remove Your Location

=== "Discord"

    ```
    !location remove
    ```

=== "Telegram"

    ```
    /location remove
    ```

## How Areas and Location Work Together

- **Area only** — alerts for anything in your subscribed areas
- **Location + distance** — alerts within N meters of your location (use `d<meters>` in tracking commands)
- **Both** — area-based tracking works alongside distance-based tracking. You can have some tracks using areas and others using distance.

### Example: Combined Tracking

=== "Discord"

    ```
    !area add downtown                          # Area for general tracking
    !location 40.7128,-74.0060                  # Location for distance tracking
    !track pikachu iv90                          # IV90+ pikachu in downtown area
    !track pikachu iv100 d500                    # Perfect pikachu within 500m of location
    !raid level5 d1000                           # Level 5 raids within 1km of location
    ```

!!! note
    If a tracking command includes `d<n>`, it uses distance from your location. If it doesn't include `d`, it uses your subscribed areas.
