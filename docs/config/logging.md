# `[logging]`

Processor log output, file rotation, and verbosity.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `level` | string | `"verbose"` | Log level: `silly`, `debug`, `verbose`, `info`, `warn`. |
| `file_logging_enabled` | bool | `true` | Write logs to a file as well as stdout. |
| `filename` | string | `"logs/processor.log"` | Log file path. |
| `max_size` | int | `50` | Max log file size in megabytes before rotation. |
| `max_age` | int | `7` | Days to keep rotated log files. |
| `max_backups` | int | `5` | Number of rotated files to retain. |
| `compress` | bool | `true` | Gzip-compress rotated files. |

## Choosing a level

- **`verbose`** — recommended starting point. Useful detail without overwhelming volume.
- **`info`** — somewhat quieter; good for stable installs.
- **`debug`** — extra detail for diagnosing issues.
- **`silly`** — maximum verbosity. Use only when actively debugging.
- **`warn`** — only warnings and errors.

See also: [`[webhookLogging]`](webhookLogging.md) for raw webhook capture.
