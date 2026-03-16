---
title: Per-area security (Communities)
nav_order: 10
layout: default
parent: Configuration
---

# Introduction

If you have Poracle on multiple servers, or have multiple map areas you may
wish to limit the areas that users can scan based on their membership levels.
This is achieved through configuration of a number of *communities*.

## Configuration
This is a little more complex to set up, but easy enough in concept to understand.  If enabled, this replaces the channel/role settings in either discord or telegram and uses these settings instead.  Now users can be a member of one or more community (stored in the new database field community_membership).
Each community can be associated with roles and channels - when users register in these channels or have the appropriate roles granted then they will be added to that community. This means they can see the given areas in !area list and add them.
In addition, you can set one locationFence which is an area which users cannot set their !location outside of.
If the strictLocation is set to true this is validated at alert generation time (I have only added to monsters, raids and eggs but will add to their others when this is proven to work).

```json
   "communities": {
      "newyork": {
        "allowedAreas": [
          "manhattan", "bronx", "brooklyn", "queens"
        ],
        "locationFence": "wholenewyork",
        "discord": {
          "channels": [
            "xx"
          ],
          "userRole": [
            "xx"
          ]
        },
        "telegram": {
          "channels": [
          ]
        }
      },
      "chicago": {
        "allowedAreas": [
          "northwest", "southside", "central"
        ],
        "locationFence": "wholechicago",
        "discord": {
          "channels": [
            "xx"
          ],
          "userRole": [
            "xx"
          ]
        },
        "telegram": {
          "channels": [
            "xx"
          ]
        }
      },
```

Note that once this is configured the roles and channels in the telegram and
discord section are ignored.

## Initial implementation

If using role based security (which is recommended with multiple areas, otherwise
there is no convenient way for users to leave), the automatic reconciliation
will fix user's communities

## Channel communities

If you are using delegated administration, you may wish to restrict a channel
to particular community membership.  You do this with the `!community` command