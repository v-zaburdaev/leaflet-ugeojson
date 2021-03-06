Leaflet uGeoJSON Layer 
=============================

## What is it?
A [Leaflet](http://leafletjs.com/) plugin to create a custom GeoJSON overlay which is updated after each drag and each zoom. 
The "u" stands for "updating".


## Installation
Just copy src/leaflet.uGeoJSON.js into your working directory.


## How to use it?
The plugin trigger an ajax POST call each time you move the map. This ajax call sends at least 5 parameters : 
* North latitude
* South latitude
* East longitude
* West longitude
* Zoom level

So you need a server that uses this parameters to generate a GeoJSON.

Here is the basic example : 

```javascript
var attr_osm = 'Map data &copy; <a href="http://openstreetmap.org/">OpenStreetMap</a> contributors',
var osm = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png');

var map = new L.Map('map').addLayer(osm).setView(new L.LatLng(52.265, 10.524), 14);

L.uGeoJSONLayer({ endpoint:"URL TO SERVER"
                }).addTo(map);
```

## What are the options?
As this layer is based on the GeoJSON layer, you can use all the original options.

Here are the additionnal options you can specify as an argument of L.uGeoJSONLayer.
* **endpoint**: Mandatory : the url of the server the plugin is going to reach for new data,

* **debug**: display debug log in the console or not. Default : false,
* **light**: remove or not data after updating. Default : true,

* **maxRequests**: the number of parallel requests allowed. Default : 5,
* **pollTime**: the time in ms between 2 updates without moving. Default : 0 (ie no automatic update), 
* **usebbox**: send the bounding box values as `bbox=southwest_lng,southwest_lat,northeast_lng,northeast_lat` instead of the default individual `south`, `north`, `east` and `west` parameters. Default : false,

* **parameters**: additional parameters to the post requests,
* **once** : allow to load the layer only once. Default : false,
* **after** : a function that is run after the data is rendered, taking the GeoJSON data object as parameter. Default : none,
* **afterFetch** : a function that called after the data is fetched, but not rendered yet. Used for accurate destroy previous rendered layers. Default : none,
* **enctype** : set POST request encodings type. Default is multipart/form-data. can be "form-data", "urlencoded" or "json"

Events:
* **refresh** : refresh layers event, can be fired ```javascript m.fireEvent("refresh");``` there m - leaflet map.

## How to use the "parameters" option?
This option can be used in 2 ways : 
* static : 
```javascript 
parameters:{toto:123}, 
```
* dynamic: 
```javacript 
parameters:{toto:{scope:window}}, 
```
* function: 
```javascript 
parameters: function(){return "some value";} 
```

In the second case, the plugin is going to look for the value of window["toto"] as the value of the post parameters toto.

## How to use "enctype" option?
This option means the request encoding type, by default it uses 'multipart/form-data' encodings, 
for multpart/form-data - enctype: "form-data" (default)
for urlencode - enctype: "urlencoded"
for json - enctype: "json"

## How to use "afterFetch" option?
If you render a some custom layers via 'onEachFeature' or 'filter' GeoJSON options, you can mark layers with some option, im called its 'type'
and when reload data from server, it's need to destroy previously created layers.
For example, i add polylines with 'type':'traffic' option (via filter), and remove it by check this option is exists.
```javascript
var afterFetch= function() {
  map.eachLayer(function(lay){
    if(lay.hasOwnProperty("options") && lay.options.hasOwnProperty("type")){
      if(lay.options.type==="traffic"){
        lay.remove();
      }
    }
  });
};
```

## How to fire refresh event
Refresh event can be fired for immediate reload geo data.
```javascript 
map.fireEvent("refresh");
```


## Dependencies
- Leaflet (tried with version 0.7.3)

## Development
This plugin is working but might not be optimal, so feel free to fork it and send PR!

## Remark
I'm not using the "movend" event as it triggers strange behavior : it can start an autocall loop! So I prefer to use dragend and zoomend.

##Thanks
I would like to thank kartenkarsten for his plugin https://github.com/kartenkarsten/leaflet-layer-overpass/ which was a base for this one.
