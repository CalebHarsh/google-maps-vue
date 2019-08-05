<template>
  <div class="GoogleMaps"></div>
</template>

<script>
import gmapsInit from "../assets/gmaps.js";
import MarkerClusterer from "@google/markerclusterer";

export default {
  name: "GoogleMaps",
  props: {
    locations: Array
  },
  async mounted() {
    try {
      const google = await gmapsInit();
      const geocoder = new google.maps.Geocoder();
      const map = new google.maps.Map(this.$el);

      geocoder.geocode({ address: "60622" }, (results, status) => {
        if (status !== "OK" || !results[0]) {
          throw new Error(status);
        }

        map.setCenter(results[0].geometry.location);
        map.fitBounds(results[0].geometry.viewport);
        map.setZoom(12);
      });

      const markerClickHandler = marker => {
        map.setCenter(marker.getPosition());
      };

      // eslint-disable-next-line
      function getColor(percent) {
        let color;
        if (percent > 0.5) {
          color = `#${((1 - (percent - 0.5) / 0.5) * 255)
            .toString(16)
            .slice(-2)}ff00`;
        } else if (percent < 0.5) {
          color = `#ff${((percent / 0.5) * 255).toString(16).slice(-2)}00`;
        } else {
          color = `#ffff00`;
        }
        return color;
      }

      const markers = this.locations.map((location, i) => {
        const marker = new google.maps.Marker({
          position: { lng: location.Latitude, lat: location.Longitude },
          map: map,
          icon: {
            fillColor: getColor((i + 1) / this.locations.length),
            fillOpacity: 1,
            strokeColor: "#000",
            strokeWeight: 1,
            scale: 1,
            path:
              "M 0,0 C -2,-20 -10,-22 -10,-30 A 10,10 0 1,1 10,-30 C 10,-22 2,-20 0,0 z"
          },
          title: location["Full Address"],
          optimized: false
        });
        marker.addListener("click", () => markerClickHandler(marker));
        return marker;
      });

      new MarkerClusterer(map, markers, {
        imagePath:
          "https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m"
      });

      const myoverlay = new google.maps.OverlayView();
      myoverlay.draw = function() {
        this.getPanes().markerLayer.id = "markerLayer";
      };
      myoverlay.setMap(map);
    } catch (error) {
      // eslint-disable-next-line
      console.error(error);
    }
  }
};
</script>

<style>
.GoogleMaps {
  width: 100vw;
  height: 100vh;
}
</style>