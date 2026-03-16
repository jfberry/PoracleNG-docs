---
title: Map tiles (static maps)
nav_order: 7
layout: default
parent: Configuration
---

{% raw %}

Poracle can be configured in a variety of different ways to support
tiles in output.  Whilst most commonly in use for discord notifications,
they are more commonly being used in some telegram contexts
(for example weather changes)

Because of the flexible dts, you can build a direct link to a tileserver
if you want, or you can use the ``{{staticMap}}`` calculated URL
which Poracle will do the heavy lifting for you.

## Discord

```json
                                "thumbnail": {
                                        "url": "{{{imgUrl}}}"
                                },
                                "image": {
                                        "url": "{{{staticMap}}}"
                                }
```

## Telegram

```json
                "template": {
                        "content": "[\u200A]({{{staticMap}}}) ...
...
                        "webpage_preview": true,
                      
```

For best results, the recommendation is to use a SwiftTileServer and
set Poracle to use the `tileservercache` option (see below) - as this
causes the tile to be pregenerated and therefore instantly available
to users.  

For telegram, the trick is to include the map as a URL link with a blank
link title - ie put ``[\u200A]({{{staticMap}}})`` in your template and
set `webpage_preview` to True. You can see this in the example DTS for
weatherchange.

# Map Providers

Poracle supports five different staticmaps services for displaying little maps below your alarms by using `{{{staticMap}}}`

## Flo's TileServerCache

That's the option for a self hosted TileServerCache server. Read more about it [here](https://github.com/123FLO321/SwiftTileserverCache). 
This is the recommended way to configure tiles in Poracle.

In your local.json, under the geocoding section set `staticProvider` to `tileservercache`

You need to copy the templates located in `tileservercache_templates/` into the `Templates/` directory of your Tileserver so
that Poracle has access to the templates it needs to generate the tiles.

When you install the tileserver, remember that Poracle just gets a tile URL
from it - so the tileserver needs to be accessible (with the same base URL) by
both Poracle and your users.

### Testing

A standard static map

```
https://tiles.yourtileserver.com/staticmap/poracle-monster?pokemon_id=3&latitude=48.12&longitude=2.12```
```

A multi-static map
```
https://tiles.yourtileserver.com/multistaticmap/poracle-multi-monster?pokemon_id=3&latitude=48.12&longitude=2.12
```

## Google Maps

### API Key

Please log in to [Google Developers console](https://console.developers.google.com/) and make sure you have the following API's enabled:  
   
* Google Maps Javascript API 
* Google Static Maps API  

In your local.json, under the geocoding section set `staticProvider` to `google` and put your key(s) in `staticKey`

These options also in geocoding influence the map request:


### Test

You can test if your API key manually by adding your key to the below urls:  

```
https://maps.googleapis.com/maps/api/staticmap?center=59.432982,24.7535747&markers=color:red|59.432982,24.7535747&maptype=roadmap&zoom=15&size=250x175&key=YOURKEYHERE
```

### Fine tuning

The configuration values for `type`, `zoom`, `width`, `height` are used when building the URL:

```javascript
data.staticMap = `https://maps.googleapis.com/maps/api/staticmap?center=${data.latitude},${data.longitude}&markers=color:red|${data.latitude},${data.longitude}&maptype=${this.config.geocoding.type}&zoom=${this.config.geocoding.zoom}&size=${this.config.geocoding.width}x${this.config.geocoding.height}&key=${this.config.geocoding.staticKey[~~(this.config.geocoding.staticKey.length * Math.random())]}`
```

## Mapquest

### API Key

Go to [the Mapquest website](https://developer.mapquest.com/), click on `Get your Free API Key`, log in and copy the Key. The limit is 15,000 tiles a month.

In your local.json, under the geocoding section set `staticProvider` to `osm` and put your key(s) in `staticKey`

### Test

```
https://www.mapquestapi.com/staticmap/v5/map?locations=59.432982,24.7535747&size=250x175&defaultMarker=marker-md-3B5998-22407F&zoom=15&key=YOURKEYHERE
```

### Fine tuning

The configuration values for `zoom`, `width`, `height` are used when building the URL:

```javascript
data.staticMap = `https://www.mapquestapi.com/staticmap/v5/map?locations=${data.latitude},${data.longitude}&size=${this.config.geocoding.width},${this.config.geocoding.height}&defaultMarker=marker-md-3B5998-22407F&zoom=${this.config.geocoding.zoom}&key=${this.config.geocoding.staticKey[~~(this.config.geocoding.staticKey.length * Math.random())]}`
```


## Mapbox

### API Key

Go to [the Mapbox website](https://www.mapbox.com/), click on `Start mapping for free`, log in and copy the Key. The limit is 50,000 a month.

In your local.json, under the geocoding section set `staticProvider` to `mapbox` and put your key(s) in `staticKey`
### Test

```
https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/url-https%3A%2F%2Fi.imgur.com%2FMK4NUzI.png(59.432982,24.7535747)/59.432982,24.7535747,15,0,0/250x175?access_token=YOURKEYHERE
```

### Fine tuning

The configuration values for `zoom`, `width`, `height` are used when building the URL:

```javascript
data.staticMap = `https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/url-https%3A%2F%2Fi.imgur.com%2FMK4NUzI.png(${data.longitude},${data.latitude})/${data.longitude},${data.latitude},${this.config.geocoding.zoom},0,0/${this.config.geocoding.width}x${this.config.geocoding.height}?access_token=${this.config.geocoding.staticKey[~~(this.config.geocoding.staticKey.length * Math.random())]}`
```
{% endraw %}
