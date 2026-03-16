---
title: About Rate Limits
nav_order: 4
layout: default
parent: Operation
---

```json
  "alertLimits": {
    "timingPeriod": 240,          // seconds over which limits should be calculated
    "dmLimit": 20,                // limit of number of messages a user can receive in the period
    "channelLimit": 40,           // limit of number of messages a channel/group can receive in the period
    "maxLimitsBeforeStop": 10,    // number of times user can hit rate limit (within 24hrs) before being stopped
    "disableOnStop": false,       // whether user should be admin disabled rather than just stopped (require admin assistance for restart)
    // allow override for specific channel ids in - id can be a channel/user id or a webhook name
    //    "limitOverride": {
    //        "id": limit,
    //        "id": limit
    //    }
    "limitOverride": {
    
    }
},
```

# Internal Rate Limits

In order to ensure that users can't build alerts which use up all your sending capacity,
and to reduce impact of hitting any global rate limits of discord or telegram, Poracle
has a built in rate limiting system.

In the above defaults you can see that every 4 minutes (240 seconds), any user that receives
more than 20 alerts will have their messaging paused until the end of the period.

Channels can have a different (usually higher) rate limit - but it still makes sense to
have a sensible limit here in case of an explosion of messages (eg raid hour, special
events, or change in spawn)

If a user hit the rate limit `maxLimitsBeforeStop` times in a 24-hour period they will be
told of their suspension and can `!start` again once they have fixed their tracking.
If you set `disableOnStop` then a user will be permanently disabled and need to come grovelling for
you to re-enable their account.

# Discord Rate Limits

Discord limits are per route; the exact limits per operation are not disclosed. If you
see messages like this:

```
2021-06-05 13:58:24 $MAIN warn: #1 Discord worker [Professor Willow#2751] 429 rate limit hit - in timeout 3498 route /channels/8**4/messages
```

in your log then you are being limited.  Note that the channel shown may refer to a channel or 
may be an individual user's direct messages - unfortunately discord shows these with an internal
channel number so they are hard to reconcile (although you will likely see lots of notifications)
being created for a user.  Running the [alarm_stats](alarm_stats.md) script can help.

With a sensible poracle rate limit you can avoid hitting most discord limits.

Adding more bots won't help you overcome rate limits (they are per route, not per bot)

## Webhook limits 

If you have a busy channel which you would like to avoid discord rate limits, you may find that swapping
to delivering via a webhook allows higher throughput.  This is a matter of some debate
and subject to change by discord.  You can try it and let us know!

# Telegram Rate Limits

Like Discord, telegram limits are per route. 

Telegram [says](https://core.telegram.org/bots/faq#my-bot-is-hitting-limits-how-do-i-avoid-this):

_"If you're sending bulk notifications to multiple users, the API will not allow more than 30 messages per second or so. Consider spreading out notifications over large intervals of 8—12 hours for best results._

_Also note that your bot will not be able to send more than 20 messages per minute to the same group._

_The API will not allow bulk notifications to more than ~30 users per second, if you go over that, you'll start getting 429 errors"_
