<template>
  
  <select id="layer-change">
    <option selected value="mapbox://styles/mapbox/streets-v11">Dark</option>
    <option value="mapbox://styles/mapbox/outdoors-v11">outdoors</option>
    <option value="mapbox://styles/mapbox/light-v10">light</option>
    <option value="mapbox://styles/mapbox/dark-v10">dark</option>
    <option value="mapbox://styles/mapbox/satellite-v9">satellite</option>
    <option value="mapbox://styles/mapbox/satellite-streets-v11">
      satellite-streets
    </option>
    <option value="mapbox://styles/mapbox/navigation-day-v1">
      navigation-day
    </option>
    <option value="mapbox://styles/mapbox/navigation-night-v1">
      navigation-night
    </option>
  </select>
  <link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v5.0.0/mapbox-gl-geocoder.css" type="text/css">
  <main class="w-screen h-screen">
    <label for="ipfile">Upload Your File</label>
    <input id="ipfile" type="file">
    <v-map
      class="w-full h-full"
      :options="data.options"
      @loaded="onMapLoaded"
    />
  </main>
</template>
<script setup lang="ts">
import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder';
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';
import mapboxgl from "mapbox-gl";
import VMap from "v-mapbox";
//drwatool
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import '@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css';

 mapboxgl.accessToken="pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng";
const data = reactive({
  options: {
    container:"map",
    // accessToken:
    //   "pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    center: [-284.8618412017822, 20.253581321936355],
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

function onMapLoaded(map: mapboxgl.Map) {
  console.log(map);
  const marker = new mapboxgl.Marker({
    draggable: true,
    color: "#333",
  });
  

  marker.setLngLat([-286.160888671875, 18.505656663040547]);
  marker.addTo(map);
  map.on("click", (e) => {
    console.log(e.lngLat.lat, e.lngLat.lng);
    new mapboxgl.Marker({
      draggable: true,
      color: getRandomColor(),
    })
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .addTo(map);
    new mapboxgl.Popup()
      .setLngLat(e.lngLat)
      .setHTML(`Country name: ${e.features[0].properties.name}`)
      .addTo(map);
  });

  const setStyle: any = document.getElementById("layer-change");
  setStyle.addEventListener("change", (event) => {
    console.log(event);
    map.setStyle(event.target.value);
  });
  map.addLayer({
    id: "points-of-interest",
    source: {
      type: "vector",
      url: "mapbox://mapbox.mapbox-streets-v8",
    },
    "source-layer": "poi_label",
    type: "circle",
    paint: {
    },
    layout: {
    },
  });

  // map.flyTo({
  //   center: [-286.160888671875, 18.505656663040547],
  //   zoom: 9,
  //   speed: 0.7,
  //   curve: 1,
  //   easing(t) {
  //     return t;
  //   },
  // });

  

  function getRandomColor() {
    var letters = "0123456789ABCDEF";
    var color = "#";
    for (var i = 0; i < 6; i++) {
      color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
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

  //new
  //    map.addControl(
  //   new MapboxGeocoder({
  //     accessToken: mapboxgl.accessToken,
  //     mapboxgl: mapboxgl
  //   })
  // );
  //demo
  map.addControl(new mapboxgl.NavigationControl());
  map.addControl(
    new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl,
    })
  );


  

//poly stataic
let pointsData = [];
  // data.allMapDataPoints.map((ele) => {
  //   let arrFormat = [ele.lat, ele.lon];
  //   pointsData.push(arrFormat);
  // });
  console.log("coordinates", pointsData);
  map.addSource("MyPolygon", {
    type: "geojson",
    data: {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          properties: {},
          geometry: {
            type: "Polygon",
            coordinates: [
              pointsData,
                [
                  [73.773193359375,
              20.02496791722277],
                  [74.72900390625,
              19.05173366503917],
                  [78.892822265625,
              18.781516724349704],
                  [78.134765625,
              20.34462694382967],
                  // [74.04931277036667, 19.266912177018096],
                  [75.56396484375,
              20.96143961409684],
                ],
            ],
          },
        },
      ],
    },
  });
  map.addLayer({
    id: "MyPolygon",
    type: "fill",
    source: "MyPolygon", // reference the data source
    layout: {},
    paint: {
      "fill-color": "#0080FF", // blue color fill
      "fill-opacity": 0.5,
    },
  });


  //line points
  map.addSource("MyLines", {
    type: "geojson",
    data: {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          properties: {},
          geometry: {
            type: "LineString",
            coordinates: [
              pointsData,
                [
                  [77.16796875,
            28.5941685062326],
                  [80.419921875,
            26.470573022375085],
            //       [78.5302734375,
            // 25.383735254706867],
            //       [ 74.6630859375,
            // 26.49024045886963],
                  // [74.04931277036667, 19.266912177018096],
            //       [75.8056640625,
            // 26.96124577052697],
                ],
            ],
          },
        },
      ],
    },
  });
  map.addLayer({
    id: "MyLines",
    type: "line",
    source: "MyLines",
    layout: {},
    paint: {
      "line-color": "#000",
      "line-width": 3,
    },
  });
  //draw tools
  var Draw = new MapboxDraw();
  map.addControl(Draw, 'top-left');
  
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
#layer-change {
  padding: 1px;
  position: fixed;
  top: 10px;
  left: 70px;
  z-index: 1;
  margin: 1px;
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
</style>