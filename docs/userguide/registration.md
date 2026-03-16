# Registration

Before you can receive alerts, you need to register with the PoracleNG bot.

## Discord

### Step 1: Register

Go to the registration channel (set up by your admin) and type:

```
!poracle
```

The bot will respond confirming your registration.

### Step 2: Start a DM

Click on the bot's name to open a direct message. You'll configure your tracking preferences in DMs with the bot.

### Step 3: Set Your Location

In your DM with the bot, set an area or location:

```
!area add downtown
```

Or set a specific location:

```
!location 40.7128,-74.0060
```

You're now ready to start tracking! See [Areas & Location](areas.md) for more details.

## Telegram

### Step 1: Register in the Group

Join the registration group (set up by your admin) and type:

```
/poracle
```

### Step 2: Start the Bot

Click on the bot's name and press **Start** (or send `/start`). This is required for the bot to send you direct messages.

!!! important
    You must click "Start" on the bot in a private chat. The bot cannot message you until you do this.

### Step 3: Set Your Location

In your private chat with the bot:

```
/area add downtown
```

Or set a specific location:

```
/location 40.7128,-74.0060
```

## Auto-Registration

If your admin has configured auto-registration:

- **Discord**: Users with specific roles are automatically registered when they receive the role
- **Telegram**: If `register_on_start = true`, sending `/start` to the bot auto-registers you

## Checking Your Status

Verify you're registered and see your current settings:

=== "Discord"

    ```
    !tracked
    ```

=== "Telegram"

    ```
    /tracked
    ```

## Unregistering

To remove your registration and all tracking data:

=== "Discord"

    ```
    !unregister
    ```

=== "Telegram"

    ```
    /unregister
    ```

!!! warning
    Unregistering deletes all your tracking subscriptions and profiles. This cannot be undone.
