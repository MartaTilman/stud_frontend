<template>
  <div>
    <h1>Real-Time Chat</h1>
    <div v-if="messages.length">
      <ul>
        <li v-for="message in messages" :key="message._id">
          <strong>{{ message.user }}:</strong> {{ message.text }}
        </li>
      </ul>
    </div>
    <div>
      <input
        type="text"
        v-model="newMessage"
        placeholder="Type your message..."
      />
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      messages: [],
      newMessage: "",
    };
  },
  async created() {
    try {
      const response = await axios.get("http://localhost:5000/messages");
      this.messages = response.data;
    } catch (error) {
      console.error("Error fetching messages:", error);
    }
  },
  methods: {
    async sendMessage() {
      try {
        if (!this.newMessage.trim()) return;
        const response = await axios.post("http://localhost:5000/sendMessage", {
          text: this.newMessage,
          user: "User", // Ovdje možeš postaviti ime trenutnog korisnika
        });
        this.newMessage = "";
        // Ažuriraj lokalni popis poruka nakon slanja poruke
        this.messages.push(response.data);
      } catch (error) {
        console.error("Error sending message:", error);
      }
    },
  },
};
</script>

<style>
/* Stilovi */
</style>
