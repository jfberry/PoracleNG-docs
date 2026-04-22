# `[area_security]`

Restricts access to different communities based on individual membership. When enabled, the community definitions override the channel and role lists in `[discord]` / `[telegram]`.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Enable area-security / multi-community mode. |
| `strict_locations` | bool | `false` | When `true`, every alert is checked against the community's `location_fence`. |

## `[[area_security.communities]]`

Defined as an array of tables. Each entry is one community.

| Field | Description |
|---|---|
| `name` | Community identifier. |
| `allowed_areas` | Fences users in this community may add via `!area add`. |
| `location_fence` | Single fence used for `strict_locations` enforcement. |
| `discord` | Inline table: `{ channels = [...], user_role = [...] }` — registration channels and gating roles. |
| `telegram` | Inline table: `{ channels = [...] }` — qualifying membership groups. |

```toml
[area_security]
enabled = true
strict_locations = false

[[area_security.communities]]
name = "newyork"
allowed_areas = ["manhattan", "bronx", "brooklyn", "queens"]
location_fence = ["wholenewyork"]
discord = { channels = ["1234567890123456"], user_role = ["9876543210987654"] }
telegram = { channels = ["-100123456789"] }

[[area_security.communities]]
name = "chicago"
allowed_areas = ["northwest", "southside", "central"]
location_fence = ["wholechicago"]
discord = { channels = ["1111111111111111"], user_role = ["2222222222222222"] }
telegram = { channels = ["-100987654321"] }
```

See also: [Area Security guide](../configuration/areasecurity.md).
