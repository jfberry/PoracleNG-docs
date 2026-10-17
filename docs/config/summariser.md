# `[summariser]`

Settings shared by summary pipelines. Currently only [quest summaries](../userguide/summaries.md) use them.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `max_per_message` | int | `25` | Split any reward group with more than this many entries into several consecutive messages so each fits Discord's embed size limit. Each part's static map shows only that part's pins. `0` = never split (very large groups will then be rejected by Discord). |

```toml
[summariser]
max_per_message = 25
```

Related settings:

- [`[tracking] quest_summary_enabled` and `quest_summary_buffer_ttl_hours`](tracking.md)
- [`[alert_limits] dm_summary_limit` and `channel_summary_limit`](alert_limits.md). A split summary counts once against these limits.
- [`[geocoding.static_map_type] questsummary`](geocoding.md#geocodingstatic_map_type)
