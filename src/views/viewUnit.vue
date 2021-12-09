<template>
    <ion-app>
        <ion-content>
           <!--  <ion-grid>
                <ion-row>
                    <ion-col size="12" size-sm></ion-col>
                    <ion-col> </ion-col>
                </ion-row>
            </ion-grid> -->
            <h1>view unit details</h1>
        </ion-content>
    </ion-app>
</template>
<script>
import { 
  IonApp, 
  // IonRow, 
  // IonCol, 
  // IonGrid, 
  IonContent } from "@ionic/vue";

import { 
  defineComponent, 
  // ref, 
  onMounted } from "vue";
// import L from "leaflet";
import "leaflet/dist/leaflet.css";

export default defineComponent({
    name: "App",
    components: {
        IonApp,
        // IonRow,
        // IonCol,
        // IonGrid,
        IonContent,
    },

    setup() {
        // let _mymap = ref(null);
        // const initMap = () => {
        //     _mymap.value = L.map("mapid").setView([10.6764195, -71.6881817], 2);
        //     L.tileLayer(
        //         "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg", {
        //             attribution: "",
        //             maxZoom: 18,
        //             id: "mapbox/streets-v11",
        //             tileSize: 512,
        //             zoomOffset: -1,
        //             accessToken: "pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
        //         }
        //     ).addTo(_mymap.value);
        // };
        onMounted(() => {
            // initMap();
        });
        return {};
    },
});
</script>