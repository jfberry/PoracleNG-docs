# Manual Installation

## Prerequisites

Install the following before proceeding:

| Requirement | Version | Notes |
|------------|---------|-------|
| **Go** | 1.25+ | [golang.org/dl](https://golang.org/dl/) |
| **MySQL** or **MariaDB** | 8.0+ / 10.6+ | Used for user data and tracking rules |
| **Git** | Any recent | For cloning the repository |

## Step 1: Create the Database

Create a MySQL/MariaDB database and user for PoracleNG:

```sql
CREATE DATABASE poracle;
CREATE USER 'poracleuser'@'localhost' IDENTIFIED BY 'poraclepassword';
GRANT ALL PRIVILEGES ON poracle.* TO 'poracleuser'@'localhost';
FLUSH PRIVILEGES;
```

!!! tip
    If you're migrating from PoracleJS, you can reuse your existing database. PoracleNG will auto-detect and adopt the existing schema on first startup.

## Step 2: Clone the Repository

```sh
git clone https://github.com/jfberry/PoracleNG.git
cd PoracleNG
```

## Step 3: Configure

Copy the example configuration and edit it:

```sh
cp config/config.example.toml config/config.toml
```

At minimum, configure:

```toml
[database]
host = "127.0.0.1"
user = "poracleuser"
password = "poraclepassword"
database = "poracle"
```

And either Discord:

```toml
[discord]
enabled = true
token = ["your-bot-token"]
guilds = ["your-guild-id"]
channels = ["registration-channel-id"]
admins = ["your-discord-user-id"]
```

Or Telegram:

```toml
[telegram]
enabled = true
token = "your-bot-token"
admins = ["your-user-id"]
channels = ["your-registration-group-id"]
```

See the [Config File Reference](../configuration/config_file.md) for all available settings.

## Step 4: Build

```sh
make build
```

This builds the Go processor binary at `processor/poracle-processor`.

## Step 5: Start

```sh
./start.sh
```

The start script verifies that `config/config.toml` exists, builds the processor binary if it's missing, then runs it. You can also use `make start`, which builds first and then runs `start.sh`.

!!! note "First Startup"
    On first startup, the processor downloads game data (pokemon names, moves, locales) into `resources/`. Rarity and shiny stats will be empty until enough pokemon sightings accumulate.

## Step 6: Verify

```sh
curl http://localhost:3030/health
```

A `200 OK` response means the processor is running.

## Step 7: Configure Your Scanner

Point your scanner's webhooks at the processor:

```
http://<your-host>:3030/
```

See [Scanner Setup](scanner.md) for detailed configuration.

## Directory Layout

After installation, the directory structure looks like:

```
PoracleNG/
  config/                  # Your configuration
    config.toml            # TOML config
    config.example.toml    # Full reference with defaults
    geofences/             # Your geofence files
    dts.json               # Your DTS templates (optional)
  fallbacks/               # Bundled defaults (used when config/ files are missing)
  examples/                # Reference files and community DTS templates
  processor/               # Go processor source and built binary
  resources/               # Downloaded game data (auto-managed)
  logs/                    # Processor log files
  scripts/                 # Migration and utility scripts
```

## Updating

To update PoracleNG:

```sh
git pull
make build
./start.sh
```

The processor will apply any pending database migrations automatically on startup.

## Running as a Service

For production use, run PoracleNG under a process manager so it restarts on failure and shuts down gracefully. PM2 is recommended because the repository includes an `ecosystem.config.js` that sets the timings PoracleNG needs to drain its Discord/Telegram delivery queues on shutdown.

### PM2 (recommended)

Install PM2 globally (one-time) and start via the bundled ecosystem file:

```sh
npm install -g pm2
pm2 start ecosystem.config.js
pm2 save
pm2 startup           # follow the printed command to enable on boot
```

The ecosystem file sets:

- `kill_timeout: 10000` — 10 seconds for graceful shutdown so the processor can drain in-flight Discord and Telegram messages
- `listen_timeout: 30000` — 30 seconds for the health check to pass before PM2 considers startup failed
- `max_restarts: 10`, `restart_delay: 5000` — bounded auto-restart

Useful day-to-day commands:

```sh
pm2 logs poracle      # follow logs
pm2 restart poracle   # restart (respects kill_timeout)
pm2 stop poracle      # stop
```

!!! warning "Don't skip the kill timeout"
    If you run `pm2 start start.sh` directly (without the ecosystem file) or use a supervisor with a short termination grace period, in-flight messages may be dropped on restart. Always use the bundled `ecosystem.config.js`, or mirror its `kill_timeout` in your own supervisor config.

### systemd (alternative)

If you prefer systemd, a minimal unit looks like:

```ini
[Unit]
Description=PoracleNG
After=network.target mysql.service

[Service]
Type=simple
User=poracle
WorkingDirectory=/path/to/PoracleNG
ExecStart=/path/to/PoracleNG/start.sh
TimeoutStopSec=15
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

`TimeoutStopSec=15` gives the same 10-second drain window with a small safety margin before systemd escalates to SIGKILL.

## Build Targets

| Command | Description |
|---------|-------------|
| `make build` | Build the processor binary |
| `make clean` | Remove processor binary |
| `make test` | Run processor tests |
| `make start` | Build and start the processor |
