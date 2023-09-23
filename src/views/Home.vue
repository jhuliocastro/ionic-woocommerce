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
        <swiper :modules="modules">
          <swiper-slide>
            <IonImg src="https://lojalacodemenina.com.br/wp-content/uploads/2023/06/mobile.png"></IonImg>
          </swiper-slide>
          <swiper-slide><IonImg src="https://lojalacodemenina.com.br/wp-content/uploads/2023/06/Mobile-1.png"></IonImg></swiper-slide>
        </swiper>
        <IonGrid>
          <IonRow>
            <IonCol size="12" v-for="product in products" :key="product.id" class="card">
              <IonImg class="card-img-product" :src="product.images[0].src"></IonImg>
              <div class="card-options-product">
                <IonLabel>{{ product.name }}</IonLabel>
                <IonLabel class="price">R$ {{ product.price }}</IonLabel>
              </div>
            </IonCol>
          </IonRow>
        </IonGrid>
        <IonImg src="https://lojalacodemenina.com.br/wp-content/uploads/2023/07/Banner-Sheila-Laco-de-menina-1.png"></IonImg>
        <IonImg src="https://lojalacodemenina.com.br/wp-content/uploads/2023/07/BATIZADO-SHEILA.png"></IonImg>
        <IonImg src="https://lojalacodemenina.com.br/wp-content/uploads/2023/07/Banner-Sheila-Laco-de-menina.png"></IonImg>
      </template>
    </ion-content>
    <IonLoading :isOpen="isLoading" message="Carregando..."></IonLoading>
  </ion-page>
</template>

<script setup lang="ts">
import { URL, CONSUMER_KEY, CONSUMER_SECRET } from '@/config';
import { Autoplay, Pagination } from 'swiper';
import { Swiper, SwiperSlide } from 'swiper/vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonImg, IonLoading, IonGrid, IonRow, IonCol, IonLabel, IonIcon } from '@ionic/vue';
import { ref, onMounted } from 'vue';
import axios from 'axios';

import 'swiper/css';
import '@ionic/vue/css/ionic-swiper.css';

const error500 = ref(false);
const products:any = ref([]);
const isLoading = ref(true);
const modules = [Autoplay, Pagination];

onMounted(async () => {
    await axios.get(`${URL}/wp-json/wc/v3/products`, {
      auth: {
        username: CONSUMER_KEY,
        password: CONSUMER_SECRET
      }
    }).then((response) => {
        products.value = response.data;
    }).catch((response) => {
        console.log(response);
        error500.value = true;
    })

    isLoading.value = false;
});

</script>

<style>
.card{
  display: flex;
  flex-direction: row;
  align-items: center;
  box-shadow: 0px 0px 3px 0px rgba(158, 158, 158, 0.75);
  margin-bottom: 10px;
}

.card-img-product{
  width: 150px;
  min-width: 150px;
}

.card-options-product{
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 8px;
}

.price{
  text-align: right;
  font-size: 18px;
  font-weight: bold;
  color: #7A511C;
}
</style>
