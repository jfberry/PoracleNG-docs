# Area Security

Area security enables multi-community setups where different communities have access to different geofence areas, controlled by Discord roles or Telegram group membership.

## Enable Area Security

```toml
[area_security]
enabled = false
strict_locations = false
```

When `strict_locations = true`, every alert is checked against the community's `location_fence` — alerts outside the fence are suppressed even if the user has tracking rules that would match.

!!! warning
    When area security is enabled, the `channels` and `user_role` settings in `[discord]` and `[telegram]` are **ignored**. Registration and access are controlled per-community instead.

## Community Definitions

Each community defines:

- **`allowed_areas`** — geofence names users in this community can use with `!area add`
- **`location_fence`** — the geofence used for strict location checking
- **Discord access** — registration channels and qualifying roles
- **Telegram access** — qualifying group memberships

### Example: Two Communities

```toml
[area_security]
enabled = true
strict_locations = false

[area_security.communities.newyork]
allowed_areas = ["manhattan", "bronx", "brooklyn", "queens"]
location_fence = "wholenewyork"

[area_security.communities.newyork.discord]
channels = ["1234567890123456"]
user_role = ["9876543210987654"]

[area_security.communities.newyork.telegram]
channels = ["-100123456789"]

[area_security.communities.chicago]
allowed_areas = ["northwest", "southside", "central"]
location_fence = "wholechicago"

[area_security.communities.chicago.discord]
channels = ["1111111111111111"]
user_role = ["2222222222222222"]

[area_security.communities.chicago.telegram]
channels = ["-100987654321"]
```

### How It Works

1. A user registers via `!poracle` in a community's registration channel
2. Their access is determined by the community configuration:
   - **Discord**: User must be in one of the community's `channels` or have one of the `user_role` roles
   - **Telegram**: User must be a member of one of the community's groups
3. The user can only `!area add` geofences listed in that community's `allowed_areas`
4. If `strict_locations = true`, alerts are filtered by the community's `location_fence`

### Multi-Community Users

Users can belong to multiple communities. Their available areas are the union of all their communities' `allowed_areas`.

## Discord Setup

For each community, specify:

- `channels` — Discord channel IDs where users can register
- `user_role` — Discord role IDs that grant automatic access

```toml
[area_security.communities.mytown.discord]
channels = ["registration-channel-id"]
user_role = ["member-role-id", "another-role-id"]
```

## Telegram Setup

For each community, specify the groups whose membership grants access:

```toml
[area_security.communities.mytown.telegram]
channels = ["-100groupid"]
```
