<template>
  <div class="chatbot">
    <div class="chat-container">
      <div v-for="(message, index) in chatMessages" :key="index" class="message" :class="{ 'user-message': message.isUser }">
        {{ message.text }}
      </div>
    </div>
    <div class="user-input">
      <input v-model="userMessage" @keyup.enter="sendMessage" placeholder="Stelle eine Frage..." class="input-field" />
      <button @click="sendMessage" class="send-button">Senden</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userMessage: '',
      chatMessages: [
        { text: 'Hallo! Stelle eine Frage.', isUser: false }
      ]
    };
  },
  methods: {
    sendMessage() {
      if (this.userMessage.trim() !== '') {
        this.chatMessages.push({ text: this.userMessage, isUser: true });
        this.getBotResponse(this.userMessage);
        this.userMessage = '';
      }
    },
    getBotResponse() {
      // Simulating a bot response without API
      const randomResponses = ["Ich bin noch im Training!", "Eine gute Frage!", "Das ist tiefgründig."];
      const randomIndex = Math.floor(Math.random() * randomResponses.length);
      const botResponse = randomResponses[randomIndex];
      
      this.chatMessages.push({ text: botResponse, isUser: false });
    }
  }
};
</script>

<style scoped>
.chatbot {
  max-width: 400px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
}

.chat-container {
  padding: 10px;
  overflow-y: scroll;
  max-height: 300px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.message {
  margin: 5px;
  padding: 8px;
  border-radius: 5px;
}

.user-message {
  background-color: #f0f0f0;
  align-self: flex-end;
}

.user-input {
  display: flex;
  margin-top: 10px;
}

.input-field {
  flex: 1;
  padding: 8px;
  border-radius: 5px 0 0 5px;
  border: 1px solid #ccc;
}

.send-button {
  border-radius: 0 5px 5px 0;
  padding: 8px 15px;
  background-color: #007bff;
  color: white;
  border: 1px solid #007bff;
  cursor: pointer;
}
</style>