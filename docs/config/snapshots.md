# `[snapshots]`

Opt-in on-disk store of the data behind each delivered alert. **Required for interactive buttons**: when a user clicks a button (mute, unsubscribe, redeliver, response templates, ...), PoracleNG looks up the snapshot of that message to know which pokemon, gym, area etc. the click refers to.

Disabled by default, so there is no disk use and no buttons unless you turn it on. With snapshots off, buttons defined in DTS are not shown.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Master switch for the snapshot store and therefore for buttons. |
| `path` | string | `"config/.cache/snapshots"` | Storage directory. Relative paths are resolved from the PoracleNG root directory. |
| `max_age_days` | int | `7` | Safety sweep: snapshots whose message expired more than this many days ago are deleted, even if their normal cleanup was missed (e.g. after a crash or restart). |
| `sweep_interval_mins` | int | `60` | How often the background sweep runs. |

```toml
[snapshots]
enabled = true
# path                = "config/.cache/snapshots"
# max_age_days        = 7
# sweep_interval_mins = 60
```

One snapshot is stored per delivered message (per user or channel). When an alert is edited, its snapshot is replaced, so it always matches what the user currently sees.

See also: [Snapshots and interactive buttons](../configuration/dtsadvanced.md#snapshots), [Muting](../userguide/mute.md).
