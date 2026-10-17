# Docker Installation

PoracleNG provides a pre-built multi-arch (amd64/arm64) Docker image containing the single Go processor binary. There is no Node.js runtime in the image and no second process to run.

| Image tag | Contents |
|-----------|----------|
| `ghcr.io/jfberry/poracleng:main` (also `:latest`) | Stable 5.2.x from the `main` branch |
| `ghcr.io/jfberry/poracleng:develop` | 5.3 development builds from the `develop` branch |
| `ghcr.io/jfberry/poracleng:<sha>` | A specific commit (short SHA) |

Images are rebuilt on every push to a branch, so pulling `:main` or `:develop` always gets the latest commit on that branch.

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

The default `docker-compose.yml` defines two services — the database and PoracleNG:

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
    image: ghcr.io/jfberry/poracleng:main     # or :develop for 5.3
    restart: always
    ports:
      - "127.0.0.1:3030:3030"   # Processor (webhook receiver + API + metrics)
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

Edit `config/config.toml`. At minimum you need to configure the listen address, database credentials, and a bot token.

#### Listen Address

Inside the container the processor must listen on all interfaces so Docker's port mapping can reach it:

```toml
[processor]
host = "0.0.0.0"
port = 3030
```

!!! warning "Change `host` from the example value"
    The image sets `PORACLE_HOST=0.0.0.0`, but that only applies when `host` is **absent** from `config.toml`. `config.example.toml` sets `host = "127.0.0.1"` explicitly, which wins — so a copied example config binds to the container's loopback and nothing outside the container can reach port 3030. Set `host = "0.0.0.0"` (or delete the line). The Compose file's `127.0.0.1:3030:3030` mapping still keeps the port private to the Docker host.

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

You should see `[start] Starting processor...` followed by the processor's own log lines (database migration status, game data loaded, bot connections). The same output is written to `logs/processor.log`.

### 7. Point Your Scanner

Configure Golbat to send webhooks to the processor:

```
http://<your-host>:3030/
```

## Ports

| Port | Purpose |
|------|---------|
| 3030 | Webhook receiver, REST API (used by PoracleWeb), `/health`, Prometheus `/metrics` |

That is the only port. Older Poracle releases also used 3031 for a Node.js alerter; that component no longer exists.

## Volumes

| Mount | Container Path | Purpose |
|-------|----------------|---------|
| `./config` | `/app/config` | Configuration files (`config.toml`, `dts.json`, geofences, etc.) |
| `./logs` | `/app/logs` | `processor.log` (and `webhooks.log` if `[webhookLogging]` is enabled) |
| `./poracledb` | `/var/lib/mysql` | MariaDB data (if using the bundled database) |

## Health Check

The container includes a built-in health check that polls `http://localhost:3030/health` every 30 seconds. You can verify it with:

```sh
docker inspect --format='{{.State.Health.Status}}' poracleng-poracle-1
```

## Updating

Back up the database first — migrations run automatically on startup and some cannot be reversed. See [Upgrading & Release Notes](../operation/upgrading.md).

```sh
docker compose exec -T poracle_db sh -c 'mariadb-dump -u root -p"$MYSQL_ROOT_PASSWORD" "$MYSQL_DATABASE"' > poracle-backup-$(date +%F).sql
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
docker build -t poracleng \
  --build-arg VERSION_COMMIT=$(git rev-parse --short=8 HEAD) \
  --build-arg VERSION_BRANCH=$(git rev-parse --abbrev-ref HEAD) \
  --build-arg VERSION_DATE=$(date -u +%Y-%m-%dT%H:%M:%SZ) \
  .
```

The `.git` directory isn't part of the Docker build context, so the commit, branch and date shown by `!version` are passed in as build args. They are optional — a bare `docker build -t poracleng .` works, and `!version` then reports the commit and date as `unknown`.

Then replace the `image:` line with `image: poracleng` in your `docker-compose.yml`.
