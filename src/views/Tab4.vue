<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Configuración</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 3</ion-title>
        </ion-toolbar>
      </ion-header>
      
       <!-- List of Text Items -->
  <ion-list>
    <ion-item>
      <ion-label>Pokémon Yellow</ion-label>
    </ion-item>
    <ion-item>
      <ion-label>Mega Man X</ion-label>
    </ion-item>
    <ion-item>
      <ion-label>The Legend of Zelda</ion-label>
    </ion-item>
    <ion-item>
      <ion-label>Pac-Man</ion-label>
    </ion-item>
    <ion-item>
      <ion-label>Super Mario World</ion-label>
    </ion-item>
  </ion-list>

  <!-- List of Input Items -->
  <ion-list>
    <ion-item>
      <ion-label>Input</ion-label>
      <ion-input></ion-input>
    </ion-item>
    <ion-item>
      <ion-label>Toggle</ion-label>
      <ion-toggle slot="end"></ion-toggle>
    </ion-item>
    <ion-item>
      <ion-label>Radio</ion-label>
      <ion-radio slot="end"></ion-radio>
    </ion-item>
    <ion-item>
      <ion-label>Checkbox</ion-label>
      <ion-checkbox slot="start"></ion-checkbox>
    </ion-item>
  </ion-list>

  <!-- List of Sliding Items -->
  <ion-list>
    <ion-item-sliding>
      <ion-item>
        <ion-label>Item</ion-label>
      </ion-item>
      <ion-item-options side="end">
        <ion-item-option @click="unread(item)">Unread</ion-item-option>
      </ion-item-options>
    </ion-item-sliding>

    <ion-item-sliding>
      <ion-item>
        <ion-label>Item</ion-label>
      </ion-item>
      <ion-item-options side="end">
        <ion-item-option @click="unread(item)">Unread</ion-item-option>
      </ion-item-options>
    </ion-item-sliding>
  </ion-list>
      
    </ion-content>
  </ion-page>
</template>

<script >
/* eslint-disable */
import { 
IonPage, 
IonHeader, 
IonToolbar, 
IonTitle, 
IonContent,
IonCheckbox, 
IonInput, 
IonItem, 
IonItemOption, 
IonItemOptions, 
IonItemSliding, 
IonList, 
IonLabel, 
IonRadio, 
IonToggle 

} from '@ionic/vue';


export default  {
  name: 'Tab3',
  components: { 
    IonHeader, 
    IonToolbar, 
    IonTitle, 
    IonContent, 
    IonPage,
    IonCheckbox, 
    IonInput, 
    IonItem, 
    IonItemOption, 
    IonItemOptions, 
    IonItemSliding, 
    IonList, 
    IonLabel, 
    IonRadio, 
    IonToggle

    }

}
</script>