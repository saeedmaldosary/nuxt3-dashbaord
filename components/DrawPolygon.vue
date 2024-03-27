<template>
  <pre>
        {{ this.cords }}
    </pre
  >
  <div id="map"></div>
</template>

<script>
import mapboxgl from "../assets/data/mapbox-gl";
import MapboxDraw from "../assets/data/mapbox-gl-draw";
import "../assets/data/mapbox-gl.css";
import "../assets/data/mapbox-gl-draw.css";

export default {
  data() {
    return { map: null, draw: null, cords: null };
  },
  mounted() {
    this.createDrawMap();
  },
  methods: {
    createDrawMap() {
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

        center: [46.8470686, 24.64250775],
        zoom: 14
      });

      this.draw = new MapboxDraw({
        displayControlsDefault: false,
        // Select which mapbox-gl-draw control buttons to add to the map.
        controls: {
          polygon: true,
          trash: true
        },
        // Set mapbox-gl-draw to draw by default.
        // The user does not have to click the polygon control button first.
        defaultMode: "draw_polygon"
      });
      this.map.addControl(this.draw);

      this.map.on("draw.create", this.updateArea);
      var self = this;
      this.map.on("draw.delete", function () {
        const elements = document.getElementsByClassName(
          "mapbox-gl-draw_polygon"
        );
        elements[0].style.display = "block";
        self.cords = null;
      });

      this.map.on("draw.update", this.updateArea);
    },
    updateArea() {
      const data = this.draw.getAll();
      if (data) {
        if (data.features[0]) {
          if (data.features[0].geometry.coordinates[0]) {
            this.cords = data.features[0].geometry.coordinates[0];
            const elements = document.getElementsByClassName(
              "mapbox-gl-draw_polygon"
            );
            elements[0].style.display = "none";
          }
        }
      }
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
