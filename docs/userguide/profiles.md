# Profiles

Profiles let you save different sets of tracking subscriptions and switch between them. For example, you might have a "home" profile and a "work" profile with different areas and tracking rules.

## How Profiles Work

- Each profile has its own set of areas, location, and tracking subscriptions
- You can create multiple named profiles
- Switching profiles changes which tracking rules are active
- The default profile is profile 1

## Managing Profiles

### List Your Profiles

=== "Discord"

    ```
    !profile list
    ```

=== "Telegram"

    ```
    /profile list
    ```

### Create/Switch to a Profile

=== "Discord"

    ```
    !profile 2                          # Switch to profile 2
    !profile 3                          # Switch to profile 3
    ```

=== "Telegram"

    ```
    /profile 2
    /profile 3
    ```

If the profile doesn't exist, it will be created.

### Name a Profile

=== "Discord"

    ```
    !profile name home                  # Name current profile "home"
    !profile name work                  # Name it "work"
    ```

=== "Telegram"

    ```
    /profile name home
    ```

### Copy a Profile

Copy all tracking from one profile to another:

=== "Discord"

    ```
    !profile copy 2                     # Copy current profile to profile 2
    ```

=== "Telegram"

    ```
    /profile copy 2
    ```

### Delete a Profile

=== "Discord"

    ```
    !profile delete 3                   # Delete profile 3
    ```

=== "Telegram"

    ```
    /profile delete 3
    ```

## Auto-Switching

Profiles can be configured to auto-switch based on time of day and day of week. Contact your admin for auto-switching setup.

## Example: Home and Work Setup

=== "Discord"

    ```
    # Set up home profile (profile 1)
    !profile 1
    !profile name home
    !location 40.7128,-74.0060
    !area add queens brooklyn
    !track pikachu iv90

    # Set up work profile (profile 2)
    !profile 2
    !profile name work
    !location 40.7580,-73.9855
    !area add manhattan
    !track pikachu iv100 d500

    # Switch between them
    !profile 1                          # Use home settings
    !profile 2                          # Use work settings
    ```
