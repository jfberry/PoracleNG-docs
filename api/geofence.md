---
title: Geofence
nav_order: 2
layout: default
parent: API
---

# Generate map of a geofence

```json
GET http://localhost:4201/api/geofence/canterbury/map

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 131
Date: Mon, 17 May 2021 19:24:06 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "url": "https://tiles.yourmap.com/staticmap/pregenerated/aAlGkfbawWlqALSAZ26gTHh5ueJrZGdBfl5wiq+q_rM=.png"
}

Response code: 200 (OK); Time: 226ms; Content length: 131 bytes

```
# Get hash of all geofences

```json
GET http://localhost:4201/api/geofence/all/hash

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 393
Date: Mon, 17 May 2021 19:24:45 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "areas": {
    "Canterbury": "4093df29d6a568c160d75d150b8e332d",
    "New Canterbury": "4093df29d6a568c160d75d150b8e332d",
    "Faversham": "8ca0884319d1270cc3f2bbeb4c0bca02",
    "ukc": "fded0ff765b9b3c71aca297f7068a27f",
    "city": "657a9800160a1c135a749e549b3d9d92",
    "bridge": "3bc88118671081b050e7713a73100e7d",
    "wincheap": "9276264ba591ecd44f223606658d4b31",
    "stdunstans": "f3fdcb38198466e86dd76b644835fb2d"
  }
}

Response code: 200 (OK); Time: 127ms; Content length: 393 bytes

```
# Generate a distance map around a point
```json
GET http://localhost:4201/api/geofence/distanceMap/51.248427/1.120635/150

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 131
Date: Mon, 17 May 2021 19:25:22 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "url": "https://tiles.yourmap.com/staticmap/pregenerated/WPpexGWN6TiG+8jy1qv86RNC+A1Y3ywmDV0I9Ihjg2Y=.png"
}

Response code: 200 (OK); Time: 4079ms; Content length: 131 bytes

```

# Get a location map centred at a point
```json
GET http://localhost:4201/api/geofence/locationMap/51.248427/1.120635

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 131
Date: Mon, 17 May 2021 19:25:53 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "url": "https://tiles.yourmap.com/staticmap/pregenerated/SKbViD1eu_IuG+We3CGUh4qiy4fg7nyLGgL_DobhVHo=.png"
}

Response code: 200 (OK); Time: 410ms; Content length: 131 bytes

```