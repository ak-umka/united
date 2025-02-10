<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Вход</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <ion-card>
        <ion-card-header>
          <ion-card-title>Введите ваш ник</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <ion-input
            v-model="nickname"
            label="Никнейм"
            placeholder="Введите ник..."
            :counter="true"
            :maxlength="20"
            fill="outline"
            clear-input
          />
          <ion-button
            expand="full"
            :disabled="!nicknameValid"
            @click="saveNickname"
          >
            Войти
          </ion-button>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonInput,
  IonButton,
} from "@ionic/vue";

const router = useRouter();
const nickname = ref<string>("");

onMounted(() => {
  const savedNick = localStorage.getItem("nickname");
  if (savedNick) {
    nickname.value = savedNick;
  }
});

const nicknameValid = computed(() => nickname.value.trim().length >= 3);

const saveNickname = () => {
  if (nicknameValid.value) {
    localStorage.setItem("nickname", nickname.value);
    router.push("/chat"); // Перенаправление в чат
  }
};
</script>
