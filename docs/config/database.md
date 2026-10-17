# `[database]`

MySQL/MariaDB connections: PoracleNG's own database, and optionally your scanner's database.

## `[database]`

PoracleNG's own database (users, tracking rules, profiles, etc.). Schema migrations run automatically at startup.

| Field | Type | Default | Description |
|---|---|---|---|
| `host` | string | `"127.0.0.1"` | Database host. |
| `port` | int | `3306` | Database port. |
| `user` | string | (required) | Database user. |
| `password` | string | `""` | Database password. |
| `database` | string | (required) | Database (schema) name. |

PoracleNG refuses to start if `user` or `database` is missing.

```toml
[database]
host = "127.0.0.1"
port = 3306
user = "poracleuser"
password = "poraclepassword"
database = "poracle"
```

## `[database.scanner]`

Optional read-only connection to your scanner's database. Without it, PoracleNG still works, but these features are unavailable:

- Nearby pokestops and gyms on static maps (`include_stops`)
- Pokestop/gym/station name lookups (e.g. [`populate_pokestop_name`](general.md))
- Looking up gyms by name or ID for `gym:` arguments in `!raid`, `!egg` and `!gym`

| Field | Type | Default | Description |
|---|---|---|---|
| `host` | string | `"127.0.0.1"` | Scanner database host. |
| `port` | int | `3306` | Scanner database port. |
| `user` | string | `""` | Scanner database user. |
| `password` | string | `""` | Scanner database password. |
| `database` | string | `""` | Scanner database name. |
| `type` | string | `"golbat"` | Scanner schema: `golbat` or `rdm`. |

The scanner connection is only used when both `user` and `database` are set. If it fails to connect, PoracleNG logs a warning and carries on without it.

```toml
[database.scanner]
host = "127.0.0.1"
port = 3306
user = "scanneruser"
password = "scannerpassword"
database = "golbat"
type = "golbat"
```

!!! note
    `scanner_type` directly under `[database]` (from older configs) is **not** read by PoracleNG. The one-off `migrate-from-poracle.js` script understands it, but in `config.toml` use `[database.scanner] type`.
