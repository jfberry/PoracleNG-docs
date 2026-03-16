---
title: Webhook Examples
nav_order: 11
layout: default
---

# Webhook Examples

## MAD

### Incoming Payload

MAD sends webhooks every 10 seconds with a batch of events.

```json
[
    {
        "type":"raid",
        "message": {...}
    },
    {
        "type":"pokemon",
        "message": {...}
    },
    ...
]
```

### Raid
```json
{
    "type": "raid",
    "message": {
      "latitude": xxx,
      "longitude": xxx,
      "level": 6,
      "pokemon_id": 18,
      "team_id": 2,
      "cp": 49801,
      "start": unixtimestamp,
      "end": unixtimestamp,
      "name": "",
      "evolution": 1,
      "move_1": 239,
      "move_2": 45,
      "gym_id": "",
      "url": "",
      "form": 0,
      "is_ex_raid_eligible": false,
      "is_exclusive": false,
      "gender": 2,
      "costume": 0
    }
  }
```

### Egg
```json
  {
    "type": "raid",
    "message": {
      "latitude": xxx,
      "longitude": xxx,
      "level": 3,
      "pokemon_id": 0,
      "team_id": 3,
      "cp": 0,
      "start": unixtimestamp,
      "end": unixtimestamp,
      "name": "",
      "evolution": 0,
      "move_1": 1,
      "move_2": 2,
      "gym_id": "",
      "url": "",
      "is_ex_raid_eligible": false,
      "is_exclusive": false
    }
  }
```

### Pokemon with IV
```json
  {
    "type": "pokemon",
    "message": {
      "encounter_id": xxx,
      "pokemon_id": 434,
      "spawnpoint_id": xxx,
      "latitude": xxx,
      "longitude": xxx,
      "disappear_time": unixtimestamp,
      "verified": true,
      "cp_multiplier": 0.481685,
      "pokemon_level": 13,
      "form": 791,
      "costume": 0,
      "cp": 380,
      "individual_attack": 8,
      "individual_defense": 8,
      "individual_stamina": 5,
      "move_1": 202,
      "move_2": 90,
      "height": 0.433733,
      "weight": 27.6922,
      "gender": 2,
      "rarity": 1,
      "base_catch": 0.51901144,
      "great_catch": 0.6664184,
      "ultra_catch": 0.76865
    }
  }
```

### Pokemon without IV
```json
  {
    "type": "pokemon",
    "message": {
      "encounter_id": xxx,
      "pokemon_id": 434,
      "spawnpoint_id": xxx,
      "latitude": xxx,
      "longitude": xxx,
      "disappear_time": unixtimestamp,
      "verified": true,
    }
  }
```

### Pokestop Invasion

```json
  {
    "type": "pokestop",
    "message": {
      "name": "",
      "pokestop_id": "",
      "latitude": xxx,
      "longitude": xxx,
      "updated": unixtimestamp,
      "last_modified": unixtimestamp,
      "url": "",
      "incident_start": unixtimestamp,
      "incident_expiration": unixtimestamp,
      "incident_grunt_type": 44
    }
  }
```


### Pokestop Lure

```json
  {
    "type": "pokestop",
    "message": {
      "name": "",
      "pokestop_id": "",
      "latitude": xxx,
      "longitude": xxx,
      "updated": unixtimestamp,
      "last_modified": unixtimestamp,
      "url": "",
      "lure_expiration": unixtimestamp,
      "lure_id": xxx
    }
  }
```

Invasions and Lures can be in the same payload.

### Quest

```json
{
    "type": "quest",
    "message": {
      "pokestop_id": "",
      "pokestop_name": "",
      "template": "CHALLENGE_CATCH_EASY",
      "pokestop_url": "",
      "conditions": [],
      "type": 4,
      "latitude": xxx,
      "longitude": xxx,
      "rewards": [
        {
          "info": {
            "item_id": 1,
            "amount": 5
          },
          "type": 2
        }
      ],
      "target": 10,
      "timestamp": unixtimestamp,
      "quest_task": ""
    }
  }
```

Quest webhooks are quite complex and always different based on the type and reward.

## RDM

Please someone tell me.
