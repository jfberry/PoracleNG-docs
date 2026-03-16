---
title: Configuration
nav_order: 1
layout: default
parent: API
---

# Get configuration values used by PoracleWeb

```json
GET http://localhost:4201/api/config/poracleWeb

HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
content-length: 392
Date: Mon, 17 May 2021 19:23:21 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "status": "ok",
  "version": "4.0.4",
  "locale": "en",
  "providerURL": "http://10.0.0.1:7070",
  "staticKey": [
    "Your MapQuest or Google Key"
  ],
  "pvpFilterMaxRank": 5000,
  "pvpFilterGreatMinCP": 1000,
  "pvpFilterUltraMinCP": 2000,
  "defaultTemplateName": 1,
  "channelNotesContainsCategory": true,
  "admins": {
    "discord": [
      "34xxxxx"
    ],
    "telegram": [
      "994xxxx"
    ]
  },
  "maxDistance": 0,
  "everythingFlagPermissions": "allow-any"
}

Response code: 200 (OK); Time: 111ms; Content length: 392 bytes

```