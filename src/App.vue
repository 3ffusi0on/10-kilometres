<template>
  <div id="app">
    <div id="cover">
      <autocomplete
        :search="search"
        placeholder="Rechercher mon adresse"
        aria-label="Rechercher mon adresse"
        :get-result-value="getResultValue"
        @submit="handleSubmit"
      ></autocomplete>
    </div>

    <div id="map" class="map-cover"></div>
  </div>
</template>

<script>
import Autocomplete from "@trevoreyre/autocomplete-vue";

export default {
  name: "App",
  components: {
    Autocomplete,
  },
  data() {
    return {
      circle: null,
      map: null,
      rad: 10000,
    };
  },
  mounted() {
    var x = 49.6333;
    var y = 5.1667;

    this.map = L.map("map");
    this.map.setView([x, y], 11);
    L.tileLayer("https://{s}.tile.osm.org/{z}/{x}/{y}.png", {
      attribution: "© OpenStreetMap",
    }).addTo(this.map);
    this.circle = new L.circle([x, y], this.rad, {
      color: "green",
      fillColor: "#06A706",
      fillOpacity: 0.5,
    });
    this.map.addLayer(this.circle);
  },

  methods: {
    search(input) {
      const url = `https://api-adresse.data.gouv.fr/search/?q=${encodeURI(
        input
      )}`;

      return new Promise((resolve) => {
        if (input.length < 3) {
          return resolve([]);
        }

        fetch(url)
          .then((response) => response.json())
          .then((data) => {
            resolve(data.features);
          });
      });
    },

    getResultValue(result) {
      return result.properties.label;
    },

    handleSubmit(result) {
      this.refreshMap(
        result.geometry.coordinates[1],
        result.geometry.coordinates[0]
      );
    },

    refreshMap(x, y) {
      this.map.removeLayer(this.circle);

      this.map.setView([x, y], 11);
      this.circle = new L.circle([x, y], this.rad, {
        color: "green",
        fillColor: "#06A706",
        fillOpacity: 0.5,
      });
      this.map.addLayer(this.circle);
    },
  },
};
</script>