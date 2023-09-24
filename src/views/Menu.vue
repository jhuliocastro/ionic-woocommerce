<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <IonImg class="logo" src="/logo.png"></IonImg>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <template v-if="error500">
        <IonTitle>Erro 500</IonTitle>
      </template>
      <template v-else>
        <IonList lines="full">
          <template v-if="itemsThirdLevel.length > 0">
            <IonListHeader>
              <IonLabel>Menu > {{ menus[keySecondLevel].title }} > {{ menus[keySecondLevel].child_items[keyThirdLevel].title }}</IonLabel>
            </IonListHeader>
            <IonItem @click="backSecondLevel()">
              <span style="flex-grow: 1;">Voltar</span>
              <IonIcon aria-hidden="true" :icon="arrowBackCircleOutline"></IonIcon>
            </IonItem>
            <IonItem v-for="(item, key) in itemsThirdLevel" :key="key">
              <span style="flex-grow: 1;">{{ item.title }}</span>
              <IonIcon v-if="item.child_items" aria-hidden="true" :icon="arrowForwardCircleOutline"></IonIcon>
            </IonItem>
          </template>
          <template v-else-if="itemsSecondLevel.length > 0">
            <IonListHeader>
              <IonLabel>Menu > {{ menus[keySecondLevel].title }}</IonLabel>
            </IonListHeader>
            <IonItem @click="backMainMenu()">
              <span style="flex-grow: 1;">Voltar</span>
              <IonIcon aria-hidden="true" :icon="arrowBackCircleOutline"></IonIcon>
            </IonItem>
            <IonItem @click="thirdLevel(key)" v-for="(item, key) in itemsSecondLevel" :key="key">
              <span style="flex-grow: 1;">{{ item.title }}</span>
              <IonIcon v-if="item.child_items" aria-hidden="true" :icon="arrowForwardCircleOutline"></IonIcon>
            </IonItem>
          </template>
          <template v-else>
            <IonListHeader>
              <IonLabel>Menu</IonLabel>
            </IonListHeader>
            <IonItem @click="secondLevel(key)" v-for="(item, key) in menus" :key="key">
              <span style="flex-grow: 1;">{{ item.title }}</span>
              <IonIcon v-if="item.child_items" aria-hidden="true" :icon="arrowForwardCircleOutline"></IonIcon>
            </IonItem>
          </template>
        </IonList>
      </template>
    </ion-content>
    <IonLoading :isOpen="isLoading" message="Carregando..."></IonLoading>
  </ion-page>
</template>

<script setup lang="ts">
import { URL } from '@/config';
import { IonListHeader, IonLabel, IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonImg, IonList, IonItem, IonIcon, IonLoading } from '@ionic/vue';
import { arrowForwardCircleOutline, arrowBackCircleOutline } from 'ionicons/icons';
import { ref, onMounted } from 'vue';
import axios from 'axios';

const url = URL;
const error500 = ref(false);
const menus:any = ref({});
const itemsSecondLevel:any = ref([]);
const itemsThirdLevel:any = ref([]);
const isLoading = ref(true);

const keySecondLevel = ref()
const keyThirdLevel = ref()

onMounted(async () => {
    await axios.get(`${url}/wp-json/menus/v1/menus/menu-principal`).then((response) => {
        menus.value = response.data.items;
    }).catch((response) => {
        console.log(response);
        error500.value = true;
    })

    isLoading.value = false;
});

function secondLevel(this: any, value:any){
  if(this.menus[value].child_items) {
    this.itemsSecondLevel = this.menus[value].child_items
    this.keySecondLevel = value
  }
}

function thirdLevel(this: any, value:any){
  if(this.menus[this.keySecondLevel].child_items[value].child_items) {
    this.itemsThirdLevel = this.menus[this.keySecondLevel].child_items[value].child_items
    this.keyThirdLevel = value
  }
}

function backMainMenu(this: any){
  this.itemsSecondLevel = []
}

function backSecondLevel(this: any){
  this.itemsThirdLevel = []
}
</script>
