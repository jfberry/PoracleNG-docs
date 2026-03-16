---
title: Log files
nav_order: 3
layout: default
parent: Operation
---
# Poracle Log files

Poracle stores it's log files in the `./logs` folder. It divides up the log messages for
easy traceability:

| Log file | Usage |
|---|---|
| general.log | General entries |
| errors.log | Copies of all log entries at **warn** or **error** level |
| controller.log | All entries by the alert controllers - these are the decision makers for sending alerts |
| discord.log | Entries relating to communication with discord |
| telegram.log | Entries relating to communication with telegram |
| webhook.log | Entries related to incoming webhooks from your scanner |

# Configuration

```json
  "logger": {
    "consoleLogLevel": "verbose",       // this is the level displayed on the screen (and perhaps in pm2 or systemd logs if you run that way)
    "logLevel": "verbose",              // this is the log level on disk, affecting all logs
    "enableLogs": {
      "webhooks": false,                // turn on hourly webhook log (can be quite large)
      "discord": true,                  // turn on discord log (for outbound messages to discord users and channels)
      "telegram": true                  // turn on telegram log (for outbound messages to telegram users, groups and channels)
    },
    "dailyLogLimit": 7,                 // the number of days to keep the daily logs (everything aside from webhooks)
    "webhookLogLimit": 12               // the number of hours to keep the webhook logs, if enabled
},
```

Log levels are: **error**, **warn**, **info**, **verbose**, **info**, **debug**, **silly**

Logs are deleted after the number of days specified in the dailyLogLimit (or hours after
webhookLogLimit in the case of webhook logs)

# Using the log files

## Tracing an individual message

If you are searching for a particular pokemon 
If you know the encounter id number of a pokemon, for example

## Poracle internal queues

Once a minute, poracle will report it's queue status at *info* level:

```
2021-05-12 09:45:23 $MAIN info: [Main] Queues: Inbound webhook 144 | Discord: 3 + 2 | Telegram: 1
2021-05-12 09:45:27 $5 info: [Worker 5] Queues: WebhookQueue is currently 0
2021-05-12 09:45:27 $1 info: [Worker 1] Queues: WebhookQueue is currently 14
2021-05-12 09:45:27 $4 info: [Worker 4] Queues: WebhookQueue is currently 5
2021-05-12 09:45:27 $2 info: [Worker 2] Queues: WebhookQueue is currently 4
2021-05-12 09:45:27 $3 info: [Worker 3] Queues: WebhookQueue is currently 0
```

The Poracle pipeline is that webhooks are received from the scanner onto the inbound
webhook queue - shown here with 144 entries.  The web endpoint is on the main thread
and performs the duplicate elimination task and feeds the entries to the worker threads
on a round-robin basis.

The WebhookQueue on the workers are the ones which process the individual entries to
determine whether alerts should be fired. This involves more work - querying the database,
expanding dts, producing tiles, looking up addresses etc.  Build ups here indicates a
performance problem while keeping up with alert volume - perhaps on your database
server.

Once decisions are made to distribute messages these are sent back to the main thread
for delivery. You will then see them in the outbound discord queue (3 above), the
discord webhook queue (2 above) or the telegram queue (1).  Build ups in these 
outbound queues indicate a delivery problem - it's possible that a bot is overwhelmed
or (more likely) you are being rate limited by discord or telegram.

These more frequent queue entries at verbose level show what is happening per
bot -- although guidance is now that you only need a single bot and should fine 
tune the concurrent senders (ask for help if you see these queue entries growing!)

```
james@madmaster:~/PoracleJS/logs$ grep TelegramQueue general.log
2021-05-18 00:44:23 $MAIN verbose: #1 TelegramQueue is currently 0
2021-05-18 00:44:23 $MAIN verbose: #1 TelegramQueue is currently 0
2021-05-18 07:37:19 $MAIN verbose: #1 TelegramQueue is currently 0
```

```
james@madmaster:~/PoracleJS/logs$ grep DiscordQueue general.log
2021-05-18 14:12:27 $MAIN verbose: #1 DiscordQueue is currently 2
2021-05-18 14:13:26 $MAIN verbose: #1 DiscordQueue is currently 0
2021-05-18 14:13:26 $MAIN verbose: DiscordQueue[Webhook] is currently 0
2021-05-18 14:14:59 $MAIN verbose: #1 DiscordQueue is currently 1
2021-05-18 14:37:21 $MAIN verbose: #1 DiscordQueue is currently 0
2021-05-18 14:38:22 $MAIN verbose: #1 DiscordQueue is currently 0
```
### Other interesting log entries

```
2021-05-12 09:45:23 $MAIN verbose: Duplicate cache stats: {"hits":7230759,"misses":524248,"keys":145,"ksize":6147,"vsize":145}
```

This is telling you about the duplicate elimination cache.  The *hits* are messages that
have been discarded; the *misses* are those which have been allowed through. You can
see that my Poracle is working hard ignoring a lot of things!