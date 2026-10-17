# Installation

This section covers installing PoracleNG from scratch. If you're migrating from PoracleJS, see the [Migration Guide](../migration/guide.md) instead.

## Installation Methods

- **[Manual Install](manual.md)** — build the processor from source with Go
- **[Docker](docker.md)** — run the processor in a container

## Additional Setup

After installation, you'll also need:

- **[Discord Bot](discord.md)** — create a Discord bot application (if using Discord)
- **[Telegram Bot](telegram.md)** — create a Telegram bot via BotFather (if using Telegram)
- **[Scanner Setup](scanner.md)** — configure Golbat or RDM to send webhooks to PoracleNG

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Go | 1.25+ for 5.2.x (`main`); 1.27+ for 5.3 (`develop`) — manual install only |
| bash + git | Any recent — manual install only (used by `make build` / `start.sh` to stamp version info) |
| MySQL / MariaDB | 8.0+ / 10.6+ |
| Scanner | Golbat (recommended) or RDM |

## Branches and Versions

| Branch | Version | Go | Docker tag |
|--------|---------|----|------------|
| `main` | 5.2.x (stable) | 1.25+ | `ghcr.io/jfberry/poracleng:main` (also `:latest`) |
| `develop` | 5.3.0-dev | 1.27+ | `ghcr.io/jfberry/poracleng:develop` |

Before moving between versions, read [Upgrading & Release Notes](../operation/upgrading.md).
