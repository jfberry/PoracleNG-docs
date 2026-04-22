# `[webhookLogging]`

Logs raw webhook bodies received from Golbat to disk. Useful for debugging, building test fixtures, and replaying webhooks against the processor.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Enable raw webhook logging. |
| `filename` | string | `"logs/webhooks.log"` | Output file path. |
| `max_size` | int | `100` | Max file size in megabytes (also rotates on the configured interval). |
| `max_age` | int | `1` | Days to keep rotated files. |
| `max_backups` | int | `12` | Number of rotated files to retain (~12h at hourly rotation). |
| `compress` | bool | `true` | Gzip-compress rotated files. |
| `rotate_interval` | int | `60` | Minutes between forced rotations. `0` = size-based only. |

!!! warning
    Webhook logs grow fast on busy installs. Keep `max_age` and `max_backups` low unless you're actively collecting fixtures.
