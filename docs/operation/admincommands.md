# Admin Commands

Admin commands are available to users listed in the `admins` config for Discord or Telegram, and to delegated admins for their targets.

## Channel Management

=== "Discord"

    ```
    !channel add                        # Register current channel for alerts
    !channel remove                     # Unregister current channel
    !channel add #channel-name          # Register a specific channel
    ```

=== "Telegram"

    ```
    /channel add                        # Register current group for alerts
    /channel remove                     # Unregister current group
    ```

Registered channels receive alerts like a user would. They need areas and tracking set up.

## User Management

=== "Discord"

    ```
    !userlist                           # List registered users
    !disable @user                      # Disable a user's alerts
    !enable @user                       # Re-enable a user's alerts
    ```

=== "Telegram"

    ```
    /userlist
    /disable <user_id>
    /enable <user_id>
    ```

## Tracking on Behalf of Users/Channels

Admins can manage tracking for any user or channel by mentioning them:

=== "Discord"

    ```
    !track pikachu iv100 @user          # Add tracking for a user
    !track pikachu iv100 #channel       # Add tracking for a channel
    !area add downtown #channel         # Add area to a channel
    !location 40.71,-74.00 #channel     # Set location for a channel
    ```

=== "Telegram"

    Admins can target specific users/groups by ID.

## Broadcasting

Send a message to all registered users:

=== "Discord"

    ```
    !broadcast Hello everyone!
    ```

=== "Telegram"

    ```
    /broadcast Hello everyone!
    ```

## Backup & Restore

=== "Discord"

    ```
    !backup                             # Download all tracking data as JSON
    !restore                            # Restore from attached JSON file
    ```

=== "Telegram"

    ```
    /backup
    /restore
    ```

## Scripts

Run predefined tracking scripts:

=== "Discord"

    ```
    !script <name>                      # Run a named script
    ```

## Identify

Get IDs for the current channel/group/user:

=== "Discord"

    ```
    !identify
    ```

=== "Telegram"

    ```
    /identify
    ```

## Community Management (Area Security)

When area security is enabled:

=== "Discord"

    ```
    !community list                     # List communities
    ```

## Testing

Send test alerts to verify configuration:

=== "Discord"

    ```
    !poracle-test pokemon
    !poracle-test raid
    !poracle-test egg
    !poracle-test quest
    !poracle-test invasion
    !poracle-test lure
    !poracle-test weather
    ```

=== "Telegram"

    ```
    /test pokemon
    /test raid
    ```
