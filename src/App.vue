<template>
  <div id="app">
    <div id="cover">
      <form method="get" @submit="search" action="#">
        <div class="tb">
          <div class="td">
            <input v-model="address" type="text" placeholder="Rechercher" required />
          </div>
          <div class="td" id="s-cover">
            <button type="submit">
              <div id="s-circle"></div>
              <span></span>
            </button>
          </div>
        </div>
      </form>
    </div>

    <div id="map" class="map-cover"></div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      circle: null,
      map: null,
      rad: 10000,
      address: "",
    };
  },
  mounted() {
    var x = 49.6333;
    var y = 5.1667;

    //This code is for simple Map
    this.map = L.map("map");
    this.map.setView([x, y], 11);
    // add an OpenStreetMap tile layer
    L.tileLayer("http://{s}.tile.osm.org/{z}/{x}/{y}.png", {
      attribution: "© OpenStreetMap contributors",
    }).addTo(this.map);
    this.circle = new L.circle([x, y], this.rad, {
      color: "green",
      fillColor: "#06A706",
      fillOpacity: 0.5,
    });
    this.map.addLayer(this.circle);
  },

  methods: {
    search() {
      axios({
        method: "get",
        url: "https://api-adresse.data.gouv.fr/search/",
        params: {
          q: this.address,
        },
      })
        .then(
          (response) => {
            var coordinates = response.data.features[0].geometry.coordinates || null;

            if (coordinates) {
              var x = coordinates[1];
              var y = coordinates[0];
              this.map.removeLayer(this.circle);

              this.map.setView([x, y], 11);
              this.circle = new L.circle([x, y], this.rad, {
                color: "green",
                fillColor: "#06A706",
                fillOpacity: 0.5,
              });
              this.map.addLayer(this.circle);
              this.address = "";
            }
          },
        );

    },
  },
};
</script>