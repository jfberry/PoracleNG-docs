# Manual Installation

## Prerequisites

Install the following before proceeding:

| Requirement | Version | Notes |
|------------|---------|-------|
| **Go** | 1.21+ | [golang.org/dl](https://golang.org/dl/) |
| **Node.js** | 18+ | [nodejs.org](https://nodejs.org/) — LTS recommended |
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

This builds the Go processor binary and runs `npm install` for the alerter. You can also build components separately:

```sh
make build-processor    # Go processor only
make install-alerter    # Node.js alerter dependencies only
```

## Step 5: Start

```sh
./start.sh
```

The start script:

1. Builds the processor and installs alerter dependencies if needed
2. Starts the processor and waits for it to pass a health check
3. Starts the alerter
4. Monitors both processes and shuts them down together on Ctrl-C

You can also use `make start` which builds first then runs `start.sh`.

!!! note "First Startup"
    On first startup, the processor downloads game data (pokemon names, moves, locales) into `resources/`. Rarity and shiny stats will be empty until enough pokemon sightings accumulate.

## Step 6: Verify

Check both components are running:

```sh
curl http://localhost:3030/health   # Processor
curl http://localhost:3031/health   # Alerter
```

## Step 7: Configure Your Scanner

Point your scanner's webhooks at the **processor**:

```
http://<your-host>:3030/
```

See [Scanner Setup](scanner.md) for detailed configuration.

## Directory Layout

After installation, the directory structure looks like:

```
PoracleNG/
  config/                  # Your configuration
    config.toml            # Shared TOML config (both components)
    config.example.toml    # Full reference with defaults
    geofences/             # Your geofence files
    dts.json               # Your DTS templates (optional)
  fallbacks/               # Bundled defaults (used when config/ files are missing)
  examples/                # Reference files and community DTS templates
  processor/               # Go processor source
  alerter/                 # Node.js alerter source
  resources/               # Downloaded game data (auto-managed)
  logs/                    # Log files from both components
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

For production use, you'll want to run PoracleNG as a systemd service or similar. Example systemd unit:

```ini
[Unit]
Description=PoracleNG
After=network.target mysql.service

[Service]
Type=simple
User=poracle
WorkingDirectory=/path/to/PoracleNG
ExecStart=/path/to/PoracleNG/start.sh
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Build Targets

| Command | Description |
|---------|-------------|
| `make build` | Build everything |
| `make build-processor` | Build Go processor only |
| `make install-alerter` | Install alerter Node.js dependencies |
| `make clean` | Remove processor binary |
| `make test` | Run processor tests |
| `make start` | Build + start both components |
