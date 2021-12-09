<template>
    <ion-page>
        <ion-header>
            <ion-toolbar>
                <ion-title>Mi Perfil</ion-title>
            </ion-toolbar>
        </ion-header>
        <ion-content :fullscreen="true">
            <ion-header collapse="condense">
                <ion-toolbar>
                    <ion-title size="large">Tab 1</ion-title>
                </ion-toolbar>
            </ion-header>
            <ion-card>
                <ion-card-header>
                    <ion-avatar>
                        <img src="https://gravatar.com/avatar/dba6bae8c566f9d4041fb9cd9ada7741?d=identicon&f=y">
                    </ion-avatar>
                    <ion-card-subtitle>Mi Perfil</ion-card-subtitle>
                    <ion-card-title>Nombre Usuario</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                </ion-card-content>
            </ion-card>
            <ion-list>
                <ion-list-header>
                    <ion-label>
                        <!-- Single Selection -->
                    </ion-label>
                </ion-list-header>
                <ion-item>
                    <ion-label>Informacion Personal</ion-label>
                    <ion-select placeholder="Select One">
                        <ion-select-option value="lorem" selected-text>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quo voluptas sint reiciendis et eveniet illum vitae qui quis debitis quaerat veniam rerum quos provident, blanditiis repellendus dolores, ad doloribus asperiores.</ion-select-option>
                    </ion-select>
                </ion-item>
                <ion-item>
                    <ion-label>Celular</ion-label>
                    <ion-select value="brown" ok-text="Okay" cancel-text="Dismiss">
                        <ion-select-option value="555" selected-text>555-5555</ion-select-option>
                    </ion-select>
                </ion-item>
                <ion-item>
                    <ion-label>Correo Electronico</ion-label>
                    <ion-select value="brown" ok-text="Okay" cancel-text="Dismiss">
                        <ion-select-option value="555" selected-text>JhonDoe@example.com</ion-select-option>
                    </ion-select>
                </ion-item>
            </ion-list>
        </ion-content>
    </ion-page>
</template>
<script>
/* eslint-disable */
import {
    IonPage,
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonCard,
    IonCardContent,
    IonCardHeader,
    IonCardSubtitle,
    IonCardTitle,
    IonAvatar,
    IonItem,
    IonLabel,
    IonList,
    IonListHeader,
    IonSelect,
    IonSelectOption
} from '@ionic/vue';

export default {
    name: 'Tab1',
    components: {
        IonHeader,
        IonToolbar,
        IonTitle,
        IonContent,
        IonPage,
        IonCard,
        IonCardContent,
        IonCardHeader,
        IonCardSubtitle,
        IonCardTitle,
        IonAvatar,
        IonItem,
        IonLabel,
        IonList,
        IonListHeader,
        IonSelect,
        IonSelectOption
    }
}
</script>