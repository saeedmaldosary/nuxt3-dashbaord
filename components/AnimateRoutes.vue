<template>
  <div>
    <v-btn class="mb-2 mr-2 mt-2" @click="resetAnimate()">Reset</v-btn>
    <div id="map"></div>
  </div>
</template>

<script>
import mapboxgl from "/_nuxt/assets/data/mapbox-gl.js";
import "/_nuxt/assets/data/mapbox-gl.css";
export default {
  mounted() {
    this.mapbox();
  },
  data() {
    return {
      map: [],
      marker: null,
      dataMap: {
        type: "FeatureCollection",
        features: [
          {
            type: "Feature",
            geometry: {
              type: "LineString",
              coordinates: []
            }
          }
        ]
      },
      coordinates: null,
      cords: {
        type: "FeatureCollection",
        features: [
          {
            type: "Feature",
            geometry: {
              type: "LineString",
              coordinates: [
                [-122.483696, 37.833818],
                [-122.483482, 37.833174],
                [-122.483396, 37.8327],
                [-122.483568, 37.832056],
                [-122.48404, 37.831141],
                [-122.48404, 37.830497],
                [-122.483482, 37.82992],
                [-122.483568, 37.829548],
                [-122.48507, 37.829446],
                [-122.4861, 37.828802],
                [-122.486958, 37.82931],
                [-122.487001, 37.830802],
                [-122.487516, 37.831683],
                [-122.488031, 37.832158],
                [-122.488889, 37.832971],
                [-122.489876, 37.832632],
                [-122.490434, 37.832937],
                [-122.49125, 37.832429],
                [-122.491636, 37.832564],
                [-122.492237, 37.833378],
                [-122.493782, 37.833683]
              ]
            }
          }
        ]
      }
    };
  },
  methods: {
    mapbox() {
      this.map = new mapboxgl.Map({
        container: "map",
        style: {
          version: 8,
          sources: {
            google: {
              type: "raster",
              tiles: [
                "https:///mt0.google.com/vt?lyrs=m&x={x}&y={y}&z={z}",
                "https:///mt1.google.com/vt?lyrs=m&x={x}&y={y}&z={z}",
                "https:///mt2.google.com/vt?lyrs=m&x={x}&y={y}&z={z}",
                "https:///mt3.google.com/vt?lyrs=m&x={x}&y={y}&z={z}"
              ]
            }
          },
          layers: [
            {
              id: "google-layer",
              source: "google",
              type: "raster",
              minzoom: 0,
              maxzoom: 22
            }
          ]
        },
        zoom: 16
      });

      this.map.on("load", () => {
        this.dataMap = this.cords;
        this.coordinates = this.dataMap.features[0].geometry.coordinates;

        this.dataMap.features[0].geometry.coordinates = [this.coordinates[0]];

        this.map.addSource("trace", {
          type: "geojson",
          data: this.dataMap
        });
        this.map.addLayer({
          id: "trace",
          type: "line",
          source: "trace",
          paint: {
            "line-color": "red",
            "line-opacity": 0.75,
            "line-width": 5
          }
        });
        this.draw();
      });
    },
    resetAnimate() {
      this.map.getSource("trace").setData({
        type: "FeatureCollection",
        features: [
          {
            type: "Feature",
            reset: false,
            geometry: {
              type: "LineString",
              coordinates: []
            }
          }
        ]
      });
      this.dataMap.features[0].geometry.coordinates = [];
      this.draw();
    },
    createCustomMarker(url) {
      var marker = document.createElement("div");
      marker.style.backgroundImage = "url(" + url + ")";
      marker.style.backgroundSize = "contain";
      marker.style.width = "30px"; // Adjust the width of the marker
      marker.style.height = "30px"; // Adjust the height of the marker
      return marker;
    },
    draw() {
      this.map.jumpTo({ center: this.coordinates[0], zoom: 16 });
      this.map.setPitch(30);

      let i = 0;

      const timer = setInterval(() => {
        if (i < this.coordinates.length) {
          this.dataMap.features[0].geometry.coordinates.push(
            this.coordinates[i]
          );

          this.map.getSource("trace").setData(this.dataMap);
          this.map.panTo(this.coordinates[i]);

          if (this.marker) {
            this.marker.remove();
          }

          var el = document.createElement("div");
          el.className = "car_marker";

          this.marker = new mapboxgl.Marker({
            element: this.createCustomMarker(
              "https://cdn-icons-png.flaticon.com/512/3774/3774270.png"
            ),
            anchor: "bottom"
          });
          this.marker.setLngLat(this.coordinates[i]);
          this.marker.addTo(this.map);

          i++;
        } else {
          window.clearInterval(timer);
        }
      }, 600);
    }
  }
};
</script>

<style scoped>
#map {
  min-width: 500px;
  min-height: 500px;
}
@media (max-width: 550px) {
  #map {
    max-width: 100%;
    min-width: auto;
  }
}
</style>
