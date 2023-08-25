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
  try {
    const { data } = await axios.get(URL + "get-messages");
    console.log(data);
    messages.value.unshift(data);
    await subscribe();
  } catch (error) {
    console.log(error,"err");
    setTimeout(() => {
      subscribe();
    }, 500);
  }
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
