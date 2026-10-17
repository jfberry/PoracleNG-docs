# `[autocreate]`

Rules for bulk channel sync: apply a `channelTemplate.json` entry across many geofences in one operation, triggered with `!autocreate sync` or `POST /api/autocreate/run`. For the channel template format, trigger options and a worked example, see [Channel Auto-create](../configuration/autocreate.md#bulk-sync-setup).

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `removal_safety_max_percent` | int | `0` | When a sync asks to remove orphaned channels, abort the removal phase if more than this percentage of cached fences would be removed. Only applies when the cache has at least 10 entries. `0` = no check. The `force` keyword on the trigger bypasses it. |

!!! warning "Built-in default is 0 (no check)"
    `config.example.toml` sets `removal_safety_max_percent = 20`, but if you leave the key out of your config, the safety check is **off**. Set it explicitly.

## `[[autocreate.rules]]`

One entry per template application. PoracleNG refuses to start if a rule is missing `name`, `guild`, `template` or `params`, or if two rules share a `name`.

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | yes | Unique rule identifier (no spaces). Used by `!autocreate sync <name>`. |
| `guild` | string | yes | Discord guild ID the channels are created in. |
| `template` | string | yes | `channelTemplate.json` entry name. |
| `filter` | string | no | Handlebars expression evaluated per fence. Empty = match all fences. |
| `params` | array(string) | yes | At least one element. Each is rendered per fence and passed to the template as positional arguments. |
| `remove_missing` | bool | no | Default `false`. Allow orphan removal for this rule when the trigger also asks for removals. |

```toml
[autocreate]
removal_safety_max_percent = 20

[[autocreate.rules]]
name           = "uk-areas"
guild          = "12345678901234567"
template       = "area"
filter         = "{{eq server \"uk\"}}"
params         = ["{{group}}", "{{name}}"]
remove_missing = false
```
