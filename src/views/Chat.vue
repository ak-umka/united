<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Чат</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="logout">Выйти</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content ref="chatContent" class="ion-padding">
      <ion-list class="ion-list">
        <ion-item v-for="message in messages" :key="message.id">
          <ion-label>
            <h2>{{ message.sender }}</h2>
            <p>{{ message.text }}</p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>

    <ion-footer>
      <ion-toolbar>
        <ion-input
          v-model="newMessage"
          placeholder="Введите сообщение..."
          @keydown.enter="sendMessage"
          fill="outline"
          clear-input
        />
        <ion-button
          expand="full"
          :disabled="!newMessage.trim()"
          @click="sendMessage"
        >
          Отправить
        </ion-button>
      </ion-toolbar>
    </ion-footer>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, computed, nextTick } from "vue";
import { useRouter } from "vue-router";
import * as signalR from "@microsoft/signalr";
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonFooter,
  IonButton,
  IonInput,
  IonList,
  IonItem,
  IonLabel,
  IonButtons,
} from "@ionic/vue";

// ⚡ URL SignalR-сервера 
const SIGNALR_URL = "http://localhost:3209";

// ⚡ Состояние чата
const router = useRouter();
const nickname = ref<string>(localStorage.getItem("nickname") || "");
const newMessage = ref<string>("");
const messages = ref<Array<{ id: string; sender: string; text: string }>>([]);

// ⚡ Подключение к SignalR
const connection = new signalR.HubConnectionBuilder()
  .withUrl(`${SIGNALR_URL}/messenger/hub`, {
    transport: signalR.HttpTransportType.WebSockets,
    withCredentials: false,
  })
  .withAutomaticReconnect()
  .build();

// 🔌 Функция для запуска соединения
const startConnection = async () => {
  try {
    await connection.start();
    console.log("✅ Подключено к SignalR");
  } catch (error) {
    console.error("❌ Ошибка подключения к SignalR:", error);
  }
};

// 📩 Обработчик входящих сообщений
const setupSignalRListeners = () => {
  connection.on("ReceiveMessage", (sender: string, text: string) => {
    messages.value.push({ id: crypto.randomUUID(), sender, text });
  });
};

// 📤 Отправка сообщения
const sendMessage = async () => {
  if (!newMessage.value.trim()) return;

  try {
    await connection.invoke("SendMessage", nickname.value, newMessage.value);
    newMessage.value = ""; // Очищаем поле ввода после успешной отправки
  } catch (error) {
    console.error("❌ Ошибка при отправке сообщения:", error);
  }
};

// 📜 Выход из чата
const logout = () => {
  localStorage.removeItem("nickname");
  router.push("/");
};

onMounted(async () => {
  if (!nickname.value) {
    router.push("/"); // Если ник не задан, отправляем на ввод
    return;
  }

  await startConnection();
  setupSignalRListeners();
});

// 🛑 Закрытие соединения при выходе со страницы
onBeforeUnmount(() => {
  connection.stop();
});
</script>

<style scoped>
ion-content {
  --background: #f5f5f5;
}

.ion-list {
  background: none;
}

ion-item {
  border-radius: 10px;
  margin: 5px;
  background: #fff;
}

ion-label h2 {
  font-weight: bold;
}

ion-label small {
  color: gray;
}
</style>
