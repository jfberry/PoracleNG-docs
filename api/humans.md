---
title: Humans
nav_order: 3
layout: default
parent: API
---

# Get details of a human configuration

Areas here are the valid areas for selection, not current areas
```json
GET http://localhost:4201/api/humans/344179542874914817

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 273
Date: Mon, 17 May 2021 19:21:49 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "areas": [
    {
      "name": "Canterbury",
      "group": ""
    },
    {
      "name": "New Canterbury",
      "group": ""
    },
    {
      "name": "Faversham",
      "group": ""
    },
    {
      "name": "ukc",
      "group": ""
    },
    {
      "name": "city",
      "group": ""
    },
    {
      "name": "bridge",
      "group": ""
    },
    {
      "name": "wincheap",
      "group": ""
    },
    {
      "name": "stdunstans",
      "group": ""
    }
  ]
}

Response code: 200 (OK); Time: 304ms; Content length: 273 bytes

```

# Check user can set location

Check location for validity (for area security specifically)
```json
GET http://localhost:4201/api/humans/1005446300/checkLocation/51.248427/1.120635

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 33
Date: Mon, 17 May 2021 19:22:38 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "locationOk": true
}

Response code: 200 (OK); Time: 129ms; Content length: 33 bytes

```

# Get delegated administration configuration for user

```json
GET http://localhost:4201/api/humans/726564415431770202/getAdministrationRoles

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 840
Date: Sun, 16 May 2021 15:25:24 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "admin": {
    "discord": {
      "channels": [
        "824257xxxxxxxx",
        "826387xxxxxxxx"
      ],
      "webhooks": [
        "poracle-test"
      ]
    },
    "telegram": {
      "channels": []
    }
  }
}

Response code: 200 (OK); Time: 139853ms; Content length: 840 bytes
```