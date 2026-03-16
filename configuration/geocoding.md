---
title: Geocoding
nav_order: 6
layout: default
parent: Configuration
---

{% raw %}

# Geocoding

Poracle supports two different geocoding services for displaying addresses in your DTS by using ```{{addr}}```, ```{{streetNumber}}```, ```{{streetName}}```, ```{{zipcode}}```, ```{{country}}```, ```{{countryCode}}```, ```{{city}}```, ```{{state}}```, ```{{stateCode}}``` and ```{{neighbourhood}}```. Setting it to `none` will set those fields to `unknown` but
will remove any third party dependency

```json
  "geocoding": {
    //  provider, providerURL - these are used for address lookups. Can be 'none',
    //   'nominatim' for a local nominatim installation (recommended) https://github.com/mediagis/nominatim-docker
    //   or google (geocoding key provides an array of google API keys)
      "provider": "none",
      "providerURL": "",
      "cacheDetail": 3,     // number of decimal places of lon/lat to use while caching geocoding (default 3 - or use 4 for 100x more detail)
 
    // geocodingKey - google keys for geolocation - can be more than one in this array and poracle will cycle
      "geocodingKey":[""],
  },
```

Poracle will, by default, cache calls to your geocoding service. The cacheDetail setting 
controls this behaviour.  You can specify how many decimal points of lat/lon to group
together as a single entry - three is the default and keeps calls to a third party
service (like google) to a reasonable level.  If you have a local nominatim service
you may want to set to 4 - which increases accuracy (at the cost of a 100x cache
size increase) - or to 0 - which will call your geocoding service for every
single posting.

## Nominatim

This is the option for a self hosted nominatim server. Read more about it [here](https://github.com/mediagis/nominatim-docker) - this is
the most widely used solution.

## Google Maps

### API Key

Please log in to [Google Developers console](https://console.developers.google.com/) and make sure you have the following API's enabled:  
   
* Google Maps Javascript API 
* Google Maps Geocoding API
* Google Reverse Geocoding API

You can test if your API key manually by adding your key to the below urls:  

### Test

#### Geocoding

```
https://maps.googleapis.com/maps/api/geocode/json?address=Tallinn+Estonia&key=YOURKEYHERE
```  

#### Reverse Geocoding

```
https://maps.googleapis.com/maps/api/geocode/json?latlng=59.432982,24.7535747&key=YOURKEYHERE
```


## OSM

OpenStreetMap offers a public nominatim server as well, but ratelimits very hard which makes it almost unuseable with PoracleJS.
{% endraw %}