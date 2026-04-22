# PoracleNG Documentation

![PoracleNG](assets/PoracleNG.png)

PoracleNG is a high-performance Pokemon Go notification system that sends configurable alerts about wild spawns, raids, quests, invasions, lures, nests, and more to Discord and Telegram.

## Architecture

PoracleNG runs as a single Go binary (the **processor**) that receives webhooks from a scanner, matches them against in-memory tracking rules, and delivers alerts to Discord and Telegram directly.

```
Golbat ──webhook──▶ Processor (Go :3030) ──▶ Discord / Telegram
                         │
                         ▼
                       MySQL
```

The processor handles webhook ingestion, in-memory matching, alert template rendering (DTS), geocoding and static map generation, message delivery, user commands, and the REST API used by [PoracleWeb](poracleWeb/index.md). It exposes Prometheus metrics and a health check on the same port.

!!! note "PoracleJS users"
    Earlier Poracle releases split the workload between a Go processor and a Node.js alerter on a separate port. PoracleNG has merged everything into the single processor binary — there is no alerter component to run, and no second port to monitor. See the [Migration Guide](migration/guide.md).

## Key Features

- **Discord and Telegram** support simultaneously — DMs, channel posting, webhooks, and groups
- **Golbat** scanner integration (default), with RDM support
- **Web interface** via [PoracleWeb](poracleWeb/index.md) for end-user tracking configuration
- **High performance** — handles millions of events per day with in-memory matching
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

- **Migrating from PoracleJS?** — Follow the [Migration Guide](migration/guide.md) to convert your existing setup.
- **New Installation** — Start with the [Installation Guide](installation/index.md).
- **Configuration** — See the [Config File Reference](configuration/config_file.md), the per-section [Config Reference](config/general.md), or use the [Poracle Config UI](https://jfberry.github.io/poracle-config/) to build your config visually.
- **User Guide** — Learn how to [set up tracking](userguide/index.md) as an end user.

## Quick Start

### Prerequisites

- Go 1.25+
- MySQL 8.0+ or MariaDB 10.6+
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

### 4. Point Golbat at the processor

Configure Golbat to send webhooks to:

```
http://<your-host>:3030/
```

See the [Manual Installation Guide](installation/manual.md) for detailed instructions.
