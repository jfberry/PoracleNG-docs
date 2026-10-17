# `[logging]`

Processor log output, file rotation, and verbosity.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `level` | string | `"info"` | Log level: `trace`, `debug`, `info`, `warn` (most to least verbose). `error` is also accepted. |
| `file_logging_enabled` | bool | `true` | Write logs to a file as well as stdout. |
| `filename` | string | `"logs/processor.log"` | Log file path, relative to the PoracleNG root directory. |
| `max_size` | int | `50` | Max log file size in megabytes before rotation. |
| `max_age` | int | `7` | Days to keep rotated log files. |
| `max_backups` | int | `5` | Number of rotated files to retain. |
| `compress` | bool | `true` | Gzip-compress rotated files. |

## Choosing a level

- **`info`**: recommended. Standard operational messages, including the periodic `[Status]` line.
- **`debug`**: adds hot-path detail for diagnosing issues.
- **`trace`**: everything. Very noisy. Use only while actively debugging.
- **`warn`**: warnings and errors only.

An unrecognised or empty value falls back to `info`.

!!! warning "Legacy level names"
    The PoracleJS-style names are still accepted: `verbose` maps to **info**, and `silly` maps to **trace**. If you had `level = "silly"` you will now get the full trace output, which is much larger than before. Switch to `info` or `debug`.

    Very old migrated configs that set `log_level` or `console_log_level` instead of `level` are still read as a fallback.

See also: [`[webhookLogging]`](webhookLogging.md) for raw webhook capture.
