# Telegram Bot Setup

To use PoracleNG with Telegram, you need to create a bot through BotFather.

## Step 1: Create a Bot with BotFather

1. Open Telegram and search for [@BotFather](https://t.me/BotFather)
2. Send `/newbot`
3. Follow the prompts to choose a name and username for your bot
4. BotFather will give you an API token — save this

!!! danger "Keep Your Token Secret"
    Never share your bot token publicly.

## Step 2: Configure Bot Settings

Send these commands to BotFather to configure your bot:

1. `/setprivacy` — select your bot, then choose **Disable**
    - This allows the bot to read messages in groups (required for group commands)
2. `/setjoingroups` — select your bot, then choose **Enable**
    - Allows the bot to be added to groups
3. `/setcommands` — select your bot, then send the command list (optional, for autocomplete)

## Step 3: Get Your IDs

You'll need your Telegram user ID and group IDs:

- **Your User ID** — send `/start` to [@userinfobot](https://t.me/userinfobot) or use the `/identify` command after Poracle is running
- **Group ID** — add the bot to your registration group, then use the `/identify` command

!!! tip
    Group IDs typically start with `-100`. If your group ID doesn't start with `-100`, it may not be a supergroup yet. Convert it to a supergroup by enabling any group admin setting.

## Step 4: Configure PoracleNG

Add the bot token and IDs to your `config/config.toml`:

```toml
[telegram]
enabled = true
token = "your-bot-token-here"
admins = ["your-user-id"]
channels = ["your-registration-group-id"]
```

## Step 5: Start the Bot

After starting PoracleNG, open a chat with your bot in Telegram and send `/start` to begin interacting with it.

Users register by sending `/poracle` in the registration group, then messaging the bot directly to configure tracking.

For more Telegram configuration options, see [Telegram Configuration](../configuration/telegram.md).
