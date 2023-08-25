<script setup>
import { ref } from "vue";
import { WS_URL } from "../constants/api";

const messages = ref([]);
const messageText = ref("");
const userName = ref("");
const socket = ref();
const connected = ref(false);

function connect() {
  socket.value = new WebSocket(WS_URL);

  socket.value.onopen = () => {
    connected.value = true;

    const message = {
      event: "connection",
      userName: userName.value,
      id: Date.now(),
    };

    socket.value.send(JSON.stringify(message));
    console.log("socket open");
  };

  socket.value.onmessage = (event) => {
    const message = JSON.parse(event.data);
    messages.value.unshift(message);
    console.log("socket message");
  };

  socket.value.onclose = () => {
    console.log("socket close");
  };

  socket.value.onerror = () => {
    console.log("socket error");
  };
}

async function sendMessage() {
  const message = {
    userName: userName.value,
    message: messageText.value,
    id: Date.now(),
    event: "message",
  };

  socket.value.send(JSON.stringify(message));
  messageText.value = "";
}
</script>

<template>
  <div class="center" v-if="connected">
    <div>
      <div class="form">
        <input v-model="messageText" placeholder="Write your message" />
        <button @click="sendMessage">Submit</button>
      </div>

      <div class="messages">
        <div class="messages" :key="mess.id" v-for="mess of messages">
          <div v-if="mess.event === 'connection'" class="connection_message">
            User {{ mess.userName }} connected
          </div>

          <div v-else class="message">
            {{ mess.userName }}. {{ mess.message }}
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="center" v-else>
    <div class="form">
      <input v-model="userName" placeholder="Write your name" />
      <button @click="connect">Sign In</button>
    </div>
  </div>
</template>
