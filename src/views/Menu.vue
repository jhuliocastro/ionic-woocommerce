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
          <template v-if="childItems.length > 0">
            <IonItem @click="backMainMenu()">
              <span style="flex-grow: 1;">Voltar</span>
              <IonIcon aria-hidden="true" :icon="arrowBackCircleOutline"></IonIcon>
            </IonItem>
            <IonItem v-for="(item, key) in childItems" :key="key">
              <span style="flex-grow: 1;">{{ item.title }}</span>
              <IonIcon v-if="item.child_items" aria-hidden="true" :icon="arrowForwardCircleOutline"></IonIcon>
            </IonItem>
          </template>
          <template v-else>
            <IonItem @click="childMenu(key)" v-for="(item, key) in menus" :key="key">
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
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonImg, IonList, IonItem, IonIcon, IonLoading } from '@ionic/vue';
import { arrowForwardCircleOutline, arrowBackCircleOutline } from 'ionicons/icons';
import { ref, onMounted } from 'vue';
import axios from 'axios';

const url = URL;
const error500 = ref(false);
const menus:any = ref({});
const childItems:any = ref([]);
const isLoading = ref(true);

onMounted(async () => {
    await axios.get(`${url}/wp-json/menus/v1/menus/menu-principal`).then((response) => {
        menus.value = response.data.items;
    }).catch((response) => {
        console.log(response);
        error500.value = true;
    })

    isLoading.value = false;
});

function childMenu(this: any, value:any){
  if(this.menus[value].child_items) {
    this.childItems = this.menus[value].child_items
  }
}

function backMainMenu(this: any){
  this.childItems = []
}
</script>
