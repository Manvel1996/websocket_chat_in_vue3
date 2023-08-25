<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { URL } from "../constants/api";

const messages = ref([]);
const inputValue = ref("");

onMounted(() => {
  subscribe();
});

async function subscribe() {
  const eventSource = new EventSource(URL + "connect");
  eventSource.onmessage = function (event) {
    const message = JSON.parse(event.data);
    messages.value.unshift(message)
  };
}

async function sendMessage() {
  await axios.post(URL + "new-messages", {
    message: inputValue.value,
    id: Date.now(),
  });
}
</script>

<template>
  <div class="center">
    <div>
      <div class="form">
        <input v-model="inputValue" />
        <button @click="sendMessage">Submit</button>
      </div>

      <div class="messages">
        <div class="message" :key="mess.id" v-for="mess of messages">
          {{ mess.message }}
        </div>
      </div>
    </div>
  </div>
</template>
