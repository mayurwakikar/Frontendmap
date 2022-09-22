<template>
  <div>
    <main class="w-screen h-screen">
      <v-map class="w-full h-full" :options="data.options" @loaded="onMapLoaded" />
      <!-- <script src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.js"></script> -->
      <link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.css" type="text/css">
      
      <!-- Adding navigation control  useless but its now-->
    <!-- <MglNavigationControl position="top-right" /> -->
    <!-- Adding GeoJSON layer -->
    <!-- <VLayerMapboxGeojson -->
      <!-- :sourceId="geoJsonSource.id" -->
      <!-- :source="geoJsonSource" -->
      <!-- layerId="myLayer" -->
      <!-- :layer="geoJsonlayer" -->
    <!-- /> -->
    </main>
  </div>
</template>

<script setup lang="ts">
//import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';
import mapboxgl from "mapbox-gl";
import VMap from "v-mapbox";
//var MapboxDirections = require('@mapbox/mapbox-gl-directions');
//import { MglNavigationControl, VLayerMapboxGeojson } from "v-mapbox";

// export default {                    //maybe delete it later.. for just adding navigations
//   components: {
//     VMap,
//     MglNavigationControl,
//     VLayerMapboxGeojson
//   },

const data = reactive({
  options: {
    accessToken:
      "pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    center: [73.8255500793457,
          18.584751949942728],
    zoom: 11,
    maxZoom: 22,
    crossSourceCollisions: false,
    failIfMajorPerformanceCaveat: false,
    attributionControl: false,
    preserveDrawingBuffer: true,
    hash: false,
    minPitch: 0,
    maxPitch: 60,
  } as mapboxgl.MapboxOptions,
});

//trying to add a control on map
// data.addControl(
// new MapboxGeocoder({
// accessToken: mapboxgl.accessToken,
// mapboxgl: mapboxgl
// })
// );

function onMapLoaded(map: mapboxgl.Map) {
  console.log(map);

  const marker = new mapboxgl.Marker({
    draggable: true,
    color: '#0000FF'
  });
  marker.setLngLat([73.8255500793457,
          18.584751949942728])
  marker.addTo(map);

  map.on('click', (e) => {

    console.log('I am clicked', e.lngLat.lat, e.lngLat.lng);
    new mapboxgl.Marker({
      draggable: true,
      color: getRandomColor(),
    }).setLngLat([e.lngLat.lng, e.lngLat.lat]).addTo(map)

    function getRandomColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    }

    // new mapboxgl.Popup()
    // .setLngLat(e.lngLat)
    // .setHTML(`Country name: ${e.features[0].properties.name}`)
    // .addTo(map);
  });

  //flyto method take to the provided LngLat 
  // map.flyTo({
  //   center: [77.255859375,
  //        28.613459424004414],
  //   zoom: 9,
  //   speed: 0.6,
  //   curve: 3,
  //   easing(t) {
  //   return t;
  //   }
  //   });

  //jumpto method
//   map.jumpTo({
//   center: [77.255859375,
//          28.613459424004414],
//   zoom: 8,
//   pitch: 45,
//   bearing: 90
// });

//const bearing = map.getBearing();

//map.rotateto will rotate map..
// map.rotateTo(50, {duration: 3000});

map.on('click', (e) => {
console.log(`A click event has occurred at ${e.lngLat}`);
});
map.on('load', () => {
const layers = map.getStyle().layers;
// Find the index of the first symbol layer in the map style.
let firstSymbolId;
for (const layer of layers) {
if (layer.type === 'symbol') {
firstSymbolId = layer.id;
break;
}
}
 //added just for experiment on layers
map.addSource('urban-areas', {
'type': 'geojson',
'data': 'https://docs.mapbox.com/mapbox-gl-js/assets/ne_50m_urban_areas.geojson'
//'https://geojson.io/#map=8/18.211/74.136' for experimental..
});
map.addLayer(
{
'id': 'urban-areas-fill',
'type': 'fill',
'source': 'urban-areas',
'layout': {},
'paint': {
'fill-color': '#f08',
'fill-opacity': 0.4
}
// This is the important part of this example: the addLayer
// method takes 2 arguments: the layer as an object, and a string
// representing another layer's name. If the other layer
// exists in the style already, the new layer will be positioned
// right before that layer in the stack, making it possible to put
// 'overlays' anywhere in the layer stack.
// Insert the layer beneath the first symbol layer.
},
firstSymbolId
);
});


//adding c/n loaction
navigator.geolocation.getCurrentPosition(successLocation ,errorLocation,{
  enableHighAccuracy:true
})
function successLocation(position) {
  console.log(position);
  // setupMap([position.coords.longitude,position.coords.latitude])
}
function errorLocation() {
  setupMap([77.255859375, 28.613459424004414])
}

function setupMap(center) {
  const map = new mapboxgl.Map({
    container:'map',
    style:"mapbox://styles/mapbox/streets-v11",
    center:center,
    zoom:10
  })

  const nav =  new mapboxgl.NavigationControl();
  map.addControl(nav);

  //plugins
  // var directions = new MapboxDirections({
  //    accessToken:  data.options.accessToken
  // });
  // map.addControl(directions,'top-left')
}


 // Add geolocate control to the map.
 map.addControl(
    new mapboxgl.GeolocateControl({
      positionOptions: {
        enableHighAccuracy: true,
      },
      // When active the map will receive updates to the device's location as it changes.
      trackUserLocation: true,
      // Draw an arrow next to the location dot to indicate which direction the device is heading.
      showUserHeading: true,
    })
  );
  // Add zoom and rotation controls to the map.
  map.addControl(new mapboxgl.NavigationControl());

  //map controls for saerching  
  // map.addControl(
  //   new MapboxGeocoder({
  // accessToken: mapboxgl.accessToken,
  // mapboxgl: mapboxgl
  // })
  //    );
}
</script>



<style>
html,
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.w-screen {
  width: 100vw;
}

.h-screen {
  height: 100vh;
}

.h-full {
  height: 100%;
}

.w-full {
  width: 100%;
}
/* For layer */
/* body { margin: 0; padding: 0; }
#map { position: absolute; top: 0; bottom: 0; width: 100%; } */

#layer-change {
  padding: 2px;
  position: fixed;
  top: 15px;
  left: 20px;
  z-index: 1;
  margin: 5px;
}
</style>




