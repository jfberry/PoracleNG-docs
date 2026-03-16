# PoracleWeb

PoracleWeb is a web-based user interface that allows end users to configure their tracking subscriptions through a browser instead of using bot commands.

## Features

- Browse and select pokemon, raids, quests, and other tracking types visually
- Manage areas and location on an interactive map
- View and edit all tracking subscriptions
- Profile management
- Works with both Discord and Telegram users

## Compatibility

PoracleWeb is compatible with PoracleNG. It connects to the processor (port 3030), which proxies API requests to the alerter. PoracleWeb only needs one URL.

## Requirements

- PoracleNG must be running with `api_secret` configured:

```toml
[alerter]
api_secret = "your-secret-key"
```

- The processor must be accessible from wherever PoracleWeb is served

## Next Steps

See the [PoracleWeb Installation Guide](install.md) for setup instructions.
