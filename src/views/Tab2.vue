<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button
            auto-hide="false"
            @click.prevent="this.openFirst()"
          ></ion-menu-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>
    <ion-content fullscreen="true">
      <ion-menu side="start" menu-id="first" content-id="main">
        <ion-header>
          <ion-toolbar color="primary">
            <ion-title>Start Menu</ion-title>
          </ion-toolbar>
        </ion-header>
        <ion-content>
          <ion-list>
            <ion-item v-for="(unit, u) in unidades_aceptables" :key="u">
              <ion-button slot="end" size="small" @click.prevent="mostrarUnidad(unit)"
                ><ion-icon name="eye-outline"></ion-icon
              ></ion-button>
              <ion-label  @click="setOpen(true, $event)">{{ unit.d.nm }}</ion-label>
            </ion-item>
          </ion-list>
          <ion-popover
            :is-open="isOpenRef"
            css-class="my-custom-class"
            :event="event"
            :translucent="true"
            @didDismiss="setOpen(false)"
          >
            <ion-content class="ion-padding"> Popover Content </ion-content>
          </ion-popover>
        </ion-content>
      </ion-menu>

      <ion-router-outlet id="main"></ion-router-outlet>
      <div id="mapid" style="width: 100%; height: 100%; z-index: 1" />
    </ion-content>
  </ion-page>
</template>
<style>
.my-custom-menu {
  --width: 500px;
}
</style>
<script>
import axios from "axios";
import {
  IonPage,
  IonContent,
  IonHeader,
  IonMenuButton,
  IonButtons,
  IonTitle,
  IonToolbar,
  IonItem,
  IonList,
  IonMenu,
  IonRouterOutlet,
  menuController,
  IonIcon,
  IonLabel,
  IonButton,
  IonPopover,
} from "@ionic/vue";
import { defineComponent, ref } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
// import ExploreContainer from "@/components/ExploreContainer.vue";

export default defineComponent({
  name: "Tab1",
  data() {
    return {
      sid: "",
      unidades: [],
      unidades_aceptables: [],
      markerByUnit: [],
      mymap: null,
      currentPopover: null,
    };
  },
  setup() {
    const isOpenRef = ref(false);
    const event = ref();
    const setOpen = (state, ev) => {
      event.value = ev;
      isOpenRef.value = state;
    };
    return { isOpenRef, setOpen, event };
  },
  components: {
    IonPage,
    IonContent,
    IonHeader,
    IonMenuButton,
    IonButtons,
    IonTitle,
    IonToolbar,
    IonItem,
    IonList,
    IonMenu,
    IonRouterOutlet,
    IonIcon,
    IonLabel,
    IonButton,
    IonPopover,
  },
  mounted() {
    this.initMap();
    this.initSession();
  },
  methods: {
    initMap() {
      this.mymap = L.map("mapid").setView([10.6764195, -71.6881817], 2);
      L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
        {
          attribution: "",
          maxZoom: 18,
          id: "mapbox/streets-v11",
          tileSize: 512,
          zoomOffset: -1,
          accessToken:
            "pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
        }
      ).addTo(this.mymap);
    },
    async initSession() {
      var model = {
        url: `https://hst-api.wialon.com/wialon/ajax.html?svc=token/login&params={"token":"5dce19710a5e26ab8b7b8986cb3c49e58C291791B7F0A7AEB8AFBFCEED7DC03BC48FF5F8"}`,
      };
      let { data } = await axios(model.url);
      this.sid = data.eid;
      this.getUnits();
    },
    async getUnits() {
      try {
        var model = {
            url: `https://hst-api.wialon.com/wialon/ajax.html?svc=core/update_data_flags&params={"spec":[{"type":"type","data":"avl_unit","flags":4611686018427387903,"mode":0}]}&sid=${this.sid}`,
          },
          _this = this;
        let { data } = await axios(model.url);
        this.unidades = data;
        this.unidades.forEach((unit) => {
          if (Object.prototype.hasOwnProperty.call(unit.d, "pos") && unit.d.pos) {
            _this.unidades_aceptables.push(unit);
            var marker = _this.markerByUnit[unit.i];
            if (marker)
              return marker
                .setLatLng([
                  this.markerByUnit[unit.i]._latlng.lat,
                  this.markerByUnit[unit.i]._latlng.lng,
                ])
                .addTo(this.mymap);
            var unitPos = unit.d.pos;
            this.mymap.setView([unitPos.y, unitPos.x], 5);
            marker = L.marker([unitPos.y, unitPos.x], {
              clickable: false,
              draggable: false,
              icon: L.icon({
                iconUrl: `https://hst-api.wialon.com${unit.d.uri}`,
                iconAnchor: [16, 16],
                shadowUrl: "",
              }),
            })
              .bindPopup("")
              .openPopup();

            // this.polyline = L.polyline([{lat: unit.d.pos.y, lng: unit.d.pos.x}], {color: 'blue'}).addTo(this.mymap);
            this.markerByUnit[unit.i] = marker;
            this.markerByUnit[unit.i].setLatLng([unitPos.y, unitPos.x]).addTo(this.mymap);
          }
        });

        setInterval(() => _this.listenEventUnits(), 1500);
      } catch (error) {
        console.log(error);
      }
    },
    async listenEventUnits() {
      try {
        var model = {
          url: `https://hst-api.wialon.com/avl_evts?sid=${this.sid}`,
        };
        let { data } = await axios(model.url);
        if (data.events.length > 0) {
          for (let index = 0; index < data.events.length; index++) {
            const element = data.events[index];
            if (element.d.pos) {
              if (this.markerByUnit[element.i]) {
                this.markerByUnit[element.i]
                  .setLatLng([element.d.pos.y, element.d.pos.x])
                  .addTo(this.mymap);
                //  this.polyline.addLatLng({lat: element.d.pos.y, lng: element.d.pos.x});
              }
            }
          }
        }
      } catch (error) {
        console.log(error);
      }
    },
    mostrarUnidad(und) {
      const handler = {
        get(target, property) {
          if (target[property]) {
            return target[property];
          } else {
            return "Not found";
          }
        },
      };

      var unidad = new Proxy(und, handler);
      var unit = this.markerByUnit[unidad.i];
      this.mymap.setView([unit._latlng.lat, unit._latlng.lng], 15);
      menuController.enable(false, "first");
      menuController.close("first");
    },
    openFirst() {
      menuController.enable(true, "first");
      menuController.open("first");
    },
  },
});
</script>
