<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <div id="mapid" style="width: 100%; height: 100%" />
    </ion-content>
  </ion-page>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import { IonPage, IonContent } from "@ionic/vue";

export default {
  name: "ExploreContainer",
  mounted() {
    this.initMap();
  },
  components: {
    IonPage,
    IonContent,
  },
  methods: {
    initMap() {
      this.mymap = L.map("mapid").setView([10.6764195, -71.6881817], 19);
      L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
        {
          //  attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors | &copy; <a href="http://gurtam.com">Gurtam</a>'
          // attribution:'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          attribution: "",
          maxZoom: 18,
          id: "mapbox/streets-v11",
          tileSize: 512,
          zoomOffset: -1,
          accessToken:
            "pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
        }
      ).addTo(this.mymap);

      // L.marker([10.6764195, -71.6881817]).addTo(this.mymap);
      //  L.Routing.control({ waypoints: [ L.latLng(4.736131, -74.021532), L.latLng(4.739855,-74.0422417) ] }).addTo(this.mymap);
    },
  },
};
</script>
