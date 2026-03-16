---
title: Other FAQ
nav_order: 5
layout: default
parent: Operation
---

Some frequently, and some not so frequently asked questions

### How do you upgrade your node?

### How do you switch to a Pull Request for testing

This would fetch a pull request (in this case 246) into a branch
we'll call telegram and change to it

```bash
git fetch origin pull/246/head:telegram
git checkout telegram
```

To refresh, if the pull-request is updated, you must switch back to
a standard branch, then you can repeat the commands above

```bash
git checkout develop
git fetch origin pull/246/head:telegram
git checkout telegram
```

### Has my config been read correctly?

This command will display the combined default.json / local.json

```shell
echo "console.log(require('config'))" | node
```

### Prettify local.json

This command will prettify local.json

```shell
echo "console.log(JSON.stringify(JSON.parse(require('strip-json-comments')(require('fs').readFileSync('config/local.json','utf8'))),null,'\t'))" | node
```

### Testing geocoding

```js
node
(new require('node-geocoder')({provider: 'openstreetmap',osmServer:'http://10.4.2.41:7070'}).reverse({lat:52,lon:1})).then(console.log)
```

### Increase heap size

Very rarely in very big systems, Poracle may require more than 2gb of 
memory

```sh
node --max-old-space-size=8192 src/app.js
```

or under PM2:
```sh
pm2 start src/app.js --node-args="--max-old-space-size=8192" --name poracle
```

# Testing that poracle is listening

```sh
curl http://127.0.0.1:3030/health
```

If everything is working poracle will respond:

# PM2 warning on startup

```2|poracle  | WARNING: NODE_APP_INSTANCE value of '0' did not match any instance config file names.
2|poracle  | WARNING: See https://github.com/lorenwest/node-config/wiki/Strict-Mode
```

Create default-0.json in config folder containing

```{}```

# How does Poracle eliminate duplicates?

* Pokemon: Encounter ID, Disappear Time, CP
* Raid/Egg: Gym, Message End, Pokemon ID
* Incident: Pokestop ID, IncidentExpiration
* Quest: Pokestop ID, Rewards

# Links for spoofing apps

These partials can be used for spoofing apps:

```json
 "Velocity": "http://pk.md/{{latitude}},{{longitude}}",
 "iPogo": "https://ipogo.app/?coords={{latitude}},{{longitude}}",
 "iTools":  "https://assets.thinkskysoft.com/discord/jump.html?type=itoolsbt&latlng={{latitude}},{{longitude}}"
```