<template>
    <ion-page>
        <ion-header>
            <ion-toolbar>
                <ion-buttons slot="start">
                    <ion-menu-button auto-hide="false" @click.prevent="this.openFirst()"></ion-menu-button>
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
                            <!-- <ion-button slot="end" size="small" @click.prevent="mostrarUnidad(unit)"> -->
                                <ion-button slot="end" size="small" @click.prevent="showDetail()">
                                <ion-icon name="eye-outline"></ion-icon>
                            </ion-button>
                            <ion-label @click="setOpen(true, unit, $event)">{{ unit.d.nm }}</ion-label>
                        </ion-item>
                        <ion-popover :is-open="isOpenRef" css-class="my-custom-class" :event="event" :translucent="true" @didDismiss="setOpen(false)">
                            <ion-content class="ion-padding ">
                                <ion-item v-for="(unit_cmds, c) in unit_selected.d.cml" :key="c">
                                    <ion-label> {{unit_cmds.n}}</ion-label>
                                    <ion-toggle slot="start" name="kiwi" color="success"></ion-toggle>
                                </ion-item>
                                <ion-item>
                                    <ion-label>Parqueo Seguro</ion-label>
                                    <ion-toggle slot="start" name="kiwi" color="success" @click="CreateGeoFence"></ion-toggle>
                                </ion-item>
                            </ion-content>
                        </ion-popover>
                    </ion-list>
                </ion-content>
            </ion-menu>
            <ion-router-outlet id="main"></ion-router-outlet>
            <div id="mapid" style="width: 100%; height: 100%; z-index: 1" />
        </ion-content>
    </ion-page>
</template>
<style>
.my-custom-menu {
    --width: 900px;
}

.popover-content {
    width: 90% !important;
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
    IonToggle,
} from "@ionic/vue";
import { defineComponent, ref, onMounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
// import ExploreContainer from "@/components/ExploreContainer.vue";

export default defineComponent({
    name: "Tab1",
    data() {
        return {
            sid: "",
            unidades: [],
            // unidades_aceptables: [],
            markerByUnit: [],

            currentPopover: null,
        };
    },
    setup() {
        const isOpenRef = ref(false);
        const event = ref();
        let unit_selected = ref({});
        let p_unit_selected = ref({});
        let _mymap = ref(null);
        let sid = ref("");
        let bact = ref("");
        let _markerByUnit = ref({});
        let unidades_aceptables = ref([]);
        let _unidades = ref([]);

        const showDetail =  () =>  {
          // location.href = "https://www.w3schools.com";
        }

        const setOpen = (state, unit = null, ev) => {
            if (state) {
                if (Object.prototype.hasOwnProperty.call(unit.d, "cml")) {
                    unit_selected.value = unit;
                }
                event.value = ev;
                isOpenRef.value = state;
            } else {
                isOpenRef.value = state;
            }
        };

        const initMap = () => {
            _mymap.value = L.map("mapid").setView([10.6764195, -71.6881817], 2);
            L.tileLayer(
                "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg", {
                    attribution: "",
                    maxZoom: 18,
                    id: "mapbox/streets-v11",
                    tileSize: 512,
                    zoomOffset: -1,
                    accessToken: "pk.eyJ1IjoibnVuZXpqdWxpb3QiLCJhIjoiY2t1NG9pNTk3MW8ydDJ4cWdpNnV4ZnZ6aSJ9.b9aMsL-D9kJ09lm4pnzzEg",
                }
            ).addTo(_mymap.value);
        };

        const CreateGeoFence = async (e) => {
            var unidad = new Proxy(unit_selected, {
                get(target, property) {
                    return target[property];
                },
            });

            if (!e.target.checked) {
                var circle = L.circle([unidad.d.pos.y, unidad.d.pos.x], 100, {
                    color: `#${((Math.random() * 0xffffff) << 0).toString(16).padStart(6, "0")}`,
                    fillColor: `#${((Math.random() * 0xffffff) << 0)
            .toString(16)
            .padStart(6, "0")}`,
                    fillOpacity: 0.5,
                });
                // await axios.post(`http://plataforma.sesagps.com/wialon/ajax.html?svc=resource/update_zone&sid=${sid.value}&params={"n":"Parqueo Seguro","t":3,"f":0,"w":100,"p":[{"x":${unidad.d.pos.x},"y":${unidad.d.pos.y},"r":100}],"itemId":${bact.value},"id":0,"callMode":"create"}`)
                await axios.post(`https://hst-api.wialon.com/wialon/ajax.html?svc=resource/update_zone&sid=${sid.value}&params={"n":"Parqueo Seguro","t":3,"f":0,"w":100,"p":[{"x":${unidad.d.pos.x},"y":${unidad.d.pos.y},"r":100}],"itemId":${bact.value},"id":0,"callMode":"create"}`);
                _mymap.value.addLayer(circle);
                menuController.enable(false, "first");
                menuController.close("first");
            }
        };

        const initSession = async () => {
            var model = {
                url: `http://plataforma.sesagps.com/wialon/ajax.html?svc=token/login&params={"token":"d3c7bde835d5ed39de184b2751e1bcb8E33D320A5E9800E70E2378A140EEAD1B5500A54F"}`,
                // url: `https://hst-api.wialon.com/wialon/ajax.html?svc=token/login&params={"token":"5dce19710a5e26ab8b7b8986cb3c49e58C291791B7F0A7AEB8AFBFCEED7DC03BC48FF5F8"}`,
            };
            let { data } = await axios(model.url);
            sid.value = data.eid;
            bact.value = data.user.bact;
            getUnits();
        };

        const getUnits = async () => {
            try {
                var model = {
                    url: `http://plataforma.sesagps.com/wialon/ajax.html?svc=core/update_data_flags&params={"spec":[{"type":"type","data":"avl_unit","flags":4611686018427387903,"mode":0}]}&sid=${sid.value}`,
                    // url: `https://hst-api.wialon.com/wialon/ajax.html?svc=core/update_data_flags&params={"spec":[{"type":"type","data":"avl_unit","flags":4611686018427387903,"mode":0}]}&sid=${sid.value}`,
                };
                let { data } = await axios(model.url);
                _unidades.value = data;
                _unidades.value.forEach((unit) => {
                    if (Object.prototype.hasOwnProperty.call(unit.d, "pos") && unit.d.pos) {
                        unidades_aceptables.value.push(unit);
                        var marker = _markerByUnit[unit.i];

                        if (marker)
                            return marker
                                .setLatLng([
                                    _markerByUnit[unit.i]._latlng.lat,
                                    _markerByUnit[unit.i]._latlng.lng,
                                ])
                                .addTo(_mymap.value);
                        var unitPos = unit.d.pos;
                        _mymap.value.setView([unitPos.y, unitPos.x], 5);
                        marker = L.marker([unitPos.y, unitPos.x], {
                                clickable: false,
                                draggable: false,
                                icon: L.icon({
                                    iconUrl: `http://plataforma.sesagps.com${unit.d.uri}`,
                                    // iconUrl: `https://hst-api.wialon.com${unit.d.uri}`,
                                    iconAnchor: [16, 16],
                                    shadowUrl: "",
                                    iconSize: [27, 38],
                                }),
                            })
                            .bindPopup("")
                            .openPopup();

                        //   // this.polyline = L.polyline([{lat: unit.d.pos.y, lng: unit.d.pos.x}], {color: 'blue'}).addTo(this.mymap);
                        _markerByUnit[unit.i] = marker;
                        _markerByUnit[unit.i].setLatLng([unitPos.y, unitPos.x]).addTo(_mymap.value);
                    }
                });

                // setInterval(() => listenEventUnits(), 1500);
            } catch (error) {
                console.log(error);
            }
        };

        // const  listenEventUnits = async() =>  {
        //   try {
        //     var model = {
        //       // url: `http://plataforma.sesagps.com/avl_evts?sid=${sid.value}`,
        //       url: `https://hst-api.wialon.com/avl_evts?sid=${sid.value}`,
        //     };
        //     let { data } = await axios(model.url);
        //     if (data.events.length > 0) {
        //       for (let index = 0; index < data.events.length; index++) {
        //         const element = data.events[index];
        //         if (element.d.pos) {
        //           if (_markerByUnit[element.i]) {
        //             _markerByUnit[element.i]
        //               .setLatLng([element.d.pos.y, element.d.pos.x])
        //               .addTo(_mymap.value);
        //             //  this.polyline.addLatLng({lat: element.d.pos.y, lng: element.d.pos.x});
        //           }
        //         }
        //       }
        //     }
        //   } catch (error) {
        //     console.log(error);
        //   }
        // };

        const mostrarUnidad = (und) => {
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
            var unit = _markerByUnit[unidad.i];
            _mymap.value.setView([unit._latlng.lat, unit._latlng.lng], 15);
            menuController.enable(false, "first");
            menuController.close("first");
        };

        const openFirst = () => {
            menuController.enable(true, "first");
            menuController.open("first");
        };

        const CmdPrenderApagarMotor = async (e) => {
            var unidad = new Proxy(unit_selected, {
                get(target, property) {
                    return target[property];
                },
            });

            if (!e.target.checked) {
                await axios.post(`http://plataforma.sesagps.com/wialon/ajax.html?svc=unit/exec_cmd&params={"itemId":${unidad.i},"commandName":"block_engine","linkType":"","param":"AT+GTOUT=SM9570,1,,,0,0,0,0,0,0,0,,0,0,,,,FFFF$","timeout":100,"flags":0}&sid=${sid.value}`)
                // await axios.post(`https://hst-api.wialon.com/wialon/ajax.html?svc=unit/exec_cmd&params={"itemId":${unidad.i},"commandName":"block_engine","linkType":"","param":"AT+GTOUT=SM9570,1,,,0,0,0,0,0,0,0,,0,0,,,,FFFF$","timeout":100,"flags":0}&sid=${sid.value}`);
            } else {
                await axios.post(`http://plataforma.sesagps.com/wialon/ajax.html?svc=unit/exec_cmd&params={"itemId":${unidad.i},"commandName":"unblock_engine","linkType":"","param":"AT+GTOUT=SM9570,0,,,0,0,0,0,0,0,0,,0,0,,,,FFFF$","timeout":100,"flags":0}&sid=${sid.value}`)
                // await axios.post(`https://hst-api.wialon.com/wialon/ajax.html?svc=unit/exec_cmd&params={"itemId":${unidad.i},"commandName":"unblock_engine","linkType":"","param":"AT+GTOUT=SM9570,0,,,0,0,0,0,0,0,0,,0,0,,,,FFFF$","timeout":100,"flags":0}&sid=${sid.value}`);
            }
        };

        onMounted(() => {
            initMap();
            initSession();
        });
        return {
            CreateGeoFence,
            event,
            isOpenRef,
            setOpen,
            unidades_aceptables,
            mostrarUnidad,
            openFirst,
            CmdPrenderApagarMotor,
            unit_selected,
            p_unit_selected,
            showDetail
        };
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
        IonToggle,
    },
});
customElements.define(
    'nav-detail',
    class NavDetail extends HTMLElement {
        connectedCallback() {
            this.innerHTML = `
          <ion-header translucent>
            <ion-toolbar>
              <ion-buttons slot="start">
                <ion-back-button defaultHref="/"></ion-back-button>
              </ion-buttons>
              <ion-title>titulo</ion-title>
            </ion-toolbar>
          </ion-header>
          <ion-content fullscreen class="ion-padding">
            // <ion-icon name="logo-${this.tech.icon}" style="color: ${this.tech.color};" size="large"></ion-icon>
            // <p>${this.tech.description}</p>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
          </ion-content>
        `;
        }
    }
);
</script>