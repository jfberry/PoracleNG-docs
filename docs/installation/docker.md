# Docker Installation

PoracleNG provides a pre-built Docker image that packages both the Go processor and Node.js alerter into a single container.

## Prerequisites

- Docker and Docker Compose
- A MySQL 8.0+ or MariaDB 10.6+ database (or use the one in the Compose file below)

## Quick Start

### 1. Create a Project Directory

```sh
mkdir poracleng && cd poracleng
```

### 2. Clone the Repository

Clone the PoracleNG repository to get the example configuration files:

```sh
git clone https://github.com/jfberry/PoracleNG.git
```

### 3. Set Up Docker Compose

Copy the example Compose file:

```sh
cp PoracleNG/docker-compose.yml.example docker-compose.yml
```

The default `docker-compose.yml` defines two services:

```yaml
services:
  poracle_db:
    image: mariadb:10.11
    restart: always
    command: ['mysqld', '--character-set-server=utf8mb4', '--collation-server=utf8mb4_unicode_ci']
    environment:
      MYSQL_ROOT_PASSWORD: poracle_secure_root_database_password
      MYSQL_DATABASE: poracle_database
      MYSQL_USER: poracle_user
      MYSQL_PASSWORD: poracle_secure_database_password
    volumes:
      - ./poracledb:/var/lib/mysql

  poracle:
    image: ghcr.io/jfberry/poracleng:latest
    restart: always
    ports:
      - "127.0.0.1:3030:3030"   # Processor (webhook receiver + API)
      # Alerter port only needed if scraping alerter Prometheus metrics directly.
      # All API calls are proxied via the processor.
      #- "127.0.0.1:3031:3031"
    depends_on:
      - poracle_db
    volumes:
      - ./config:/app/config
      - ./logs:/app/logs
```

!!! warning "Change the Database Passwords"
    Update `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, and the matching values in your `config.toml` before starting.

### 4. Populate the Config Folder

The `config/` directory is mounted from outside the container, so you need to populate it before starting. Copy the example config and any other files you need from the cloned repository:

```sh
mkdir -p config
cp PoracleNG/config/config.example.toml config/config.toml
cp -r PoracleNG/config/geofences config/
```

The Docker image includes built-in fallbacks for `dts.json`, `partials.json`, `pokemonAlias.json`, and `testdata.json`, so you only need to add these to your `config/` folder if you want to customise them. You can copy the defaults from the repository as a starting point:

```sh
cp PoracleNG/fallbacks/dts.json config/
```

### 5. Edit Your Configuration

Edit `config/config.toml`. At minimum you need to configure the internal connections, database credentials, and a bot token.

#### Internal Connections

Both the processor and alerter run inside the same container, so they communicate via `localhost`. The defaults in the example config are already correct for Docker:

```toml
[processor]
host = "0.0.0.0"                        # Listen on all interfaces (required for Docker port mapping)
port = 3030
alerter_url = "http://localhost:3031"    # Alerter is in the same container

[alerter]
host = "127.0.0.1"                      # Localhost only — not exposed outside the container
port = 3031
processor_url = "http://localhost:3030"  # Processor is in the same container
```

The processor listens on `0.0.0.0` so that Docker can map its port to the host. The alerter listens on `127.0.0.1` since it only needs to be reached by the processor within the same container.

#### Database

If you are using the bundled MariaDB service from the Compose file, set the host to the service name:

```toml
[database]
host = "poracle_db"
port = 3306
user = "poracle_user"
password = "poracle_secure_database_password"
database = "poracle_database"
```

!!! tip "Sharing a Database Server"
    Sharing a MariaDB/MySQL instance with other tools (such as Golbat or Dragonite) is fine and even encouraged — just make sure PoracleNG has its own dedicated database. Do not point it at another tool's database.

#### Bot Token

```toml
[discord]
enabled = true
token = ["your-bot-token"]
guilds = ["your-guild-id"]
channels = ["registration-channel-id"]
admins = ["your-discord-user-id"]
```

See the [Config File Reference](../configuration/config_file.md) for all available settings.

### 6. Start

```sh
docker compose up -d
```

Check the logs to confirm everything started:

```sh
docker compose logs -f poracle
```

You should see output from both `[processor]` and `[alerter]` components.

### 7. Point Your Scanner

Configure Golbat to send webhooks to the processor:

```
http://<your-host>:3030/
```

## Ports

| Port | Component | Purpose |
|------|-----------|---------|
| 3030 | Processor | Webhook receiver, API, Prometheus metrics |
| 3031 | Alerter | Internal — only expose if scraping alerter metrics directly |

All API requests (including those used by PoracleWeb) go through port 3030 — the processor proxies `/api/` requests to the alerter.

## Volumes

| Mount | Container Path | Purpose |
|-------|----------------|---------|
| `./config` | `/app/config` | Configuration files (`config.toml`, `dts.json`, geofences, etc.) |
| `./logs` | `/app/logs` | Log files from both components |
| `./poracledb` | `/var/lib/mysql` | MariaDB data (if using the bundled database) |

## Health Check

The container includes a built-in health check that polls `http://localhost:3030/health` every 30 seconds. You can verify it with:

```sh
docker inspect --format='{{.State.Health.Status}}' poracleng-poracle-1
```

## Updating

```sh
docker compose pull
docker compose up -d
```

## Using an External Database

If you already have a MariaDB/MySQL instance, remove the `poracle_db` service and `depends_on` from `docker-compose.yml`, then point your `config.toml` at your existing database host.

Create a database and user for PoracleNG on your existing instance:

```sql
CREATE DATABASE poracle_database;
CREATE USER 'poracle_user'@'%' IDENTIFIED BY 'poracle_secure_database_password';
GRANT ALL PRIVILEGES ON poracle_database.* TO 'poracle_user'@'%';
FLUSH PRIVILEGES;
```

## Building the Image Locally

If you want to build the image yourself instead of using the pre-built one:

```sh
cd PoracleNG
docker build -t poracleng .
```

Then replace `image: ghcr.io/jfberry/poracleng:latest` with `image: poracleng` in your `docker-compose.yml`.
