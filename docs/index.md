# PoracleNG Documentation

![PoracleNG](assets/PoracleNG.png)

PoracleNG is a high-performance Pokemon Go notification system that sends configurable alerts about wild spawns, raids, quests, invasions, lures, nests, and more to Discord and Telegram.

## Architecture

PoracleNG splits the workload into two components:

```
Golbat ──webhook──▶ Processor (Go :3030) ──matched──▶ Alerter (Node.js :3031) ──▶ Discord / Telegram
                         │                                │
                         │◀──── POST /api/reload ──────────│
                         │                                │
                         ▼                                ▼
                    MySQL (read)                     MySQL (read/write)
```

- **Processor** (Go) — receives raw webhooks from Golbat, matches them against all user tracking rules in memory, and forwards only matched results to the alerter. Exposes Prometheus metrics and weather/rarity APIs.
- **Alerter** (Node.js) — receives pre-matched results, renders alert templates (DTS), performs geocoding and static map generation, and delivers messages to Discord and Telegram. Handles all user commands.

## Key Features

- **Discord and Telegram** support simultaneously — DMs, channel posting, webhooks, and groups
- **Golbat** scanner integration (default), with RDM support
- **Web interface** via [PoracleWeb](poracleWeb/index.md) for end-user tracking configuration
- **High performance** — Go processor handles millions of events per day with in-memory matching
- **Rate management** with per-route back-off and automatic user suspension for overly broad tracking
- **Flexible tracking** — by area, distance from location, or both, with multiple subscription categories
- **PVP tracking** — Great League, Ultra League, Little League rank-based alerts
- **Automatic message cleanup** — expired alerts are deleted
- **Fully configurable alert styles** with Handlebars templates and multiple template sets
- **Multi-language** support with user-switchable locales
- **Switchable profiles** for different tracking needs (e.g. home vs office) with time-based auto-switching
- **Custom static maps** including tileserver cache, multi-maps, and time-of-day styles
- **Weather forecasting** with AccuWeather integration and weather inference from pokemon boosts
- **Area security** for multi-community setups with per-community access control
- **Prometheus metrics** and health check endpoints for monitoring
- **Koji integration** for geofence management
- **GeoJSON and Poracle format** geofence support

## Getting Started

<div class="grid cards" markdown>

-   :material-swap-horizontal:{ .lg .middle } **Migrating from PoracleJS?**

    ---

    Follow the [Migration Guide](migration/guide.md) to convert your existing setup.

-   :material-download:{ .lg .middle } **New Installation**

    ---

    Start with the [Installation Guide](installation/index.md) to get PoracleNG running.

-   :material-cog:{ .lg .middle } **Configuration**

    ---

    See the [Configuration Reference](configuration/config_file.md) for all settings.

-   :material-account:{ .lg .middle } **User Guide**

    ---

    Learn how to [set up tracking](userguide/index.md) as an end user.

</div>

## Quick Start

### Prerequisites

- Go 1.21+ (processor)
- Node.js 18+ (alerter)
- MySQL 8.0+ or MariaDB
- A Golbat instance sending webhooks

### 1. Configure

```sh
cp config/config.example.toml config/config.toml
```

At minimum, set your database credentials and Discord/Telegram bot token:

```toml
[database]
host = "127.0.0.1"
user = "poracleuser"
password = "poraclepassword"
database = "poracle"

[discord]
enabled = true
token = ["your-bot-token"]
guilds = ["your-guild-id"]
channels = ["registration-channel-id"]
admins = ["your-discord-user-id"]
```

### 2. Build

```sh
make build
```

### 3. Start

```sh
./start.sh
```

### 4. Point Golbat at the Processor

Configure Golbat to send webhooks to the **processor** (not the alerter):

```
http://<your-host>:3030/
```

See the [Manual Installation Guide](installation/manual.md) for detailed instructions.
