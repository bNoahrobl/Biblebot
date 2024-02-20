<template>
  <div id="app">
    <div class="chat-container">
      <div class="chat-messages">
        <div v-for="(message, index) in messages" :key="index" class="message">
          <p :class="{ 'user-message': message.sender === 'user', 'bot-message': message.sender === 'bot' }">{{ message.text }}</p>
        </div>
      </div>
      <div class="input-container">
        <input v-model="userInput" @keyup.enter="sendMessage" type="text" placeholder="Type your message..." />
        <button @click="sendMessage">Send</button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
//import OpenAI from 'openai';


export default {
  name: 'App',
  setup() {
    const userInput = ref('');
    const messages = ref([]);

    const sendMessage = async () => {
      const userMessage = userInput.value.trim();
      if (userMessage !== '') {
        messages.value.push({ text: userMessage, sender: 'user' });
        try {
          const response = await fetch('https://api.openai.com/v1/chat/completions ', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              // Replace with your valid GPT-4 API key
              Authorization: 'Bearer Insert-Key',
            },
            body: JSON.stringify({ model: 'gpt-3.5-turbo', messages: [{ role: 'user', content: userMessage }] }),          
          });
          
          if (!response.ok) {
            throw new Error('Failed to fetch response from GPT-4 API');
            
          }
          console.log(data.response);
          const data = await response.json();
          messages.value.push({ text: data.response, sender: 'bot' });
        } catch (error) {
          console.error('Error fetching response:', error);
          messages.value.push({ text: 'An error occurred while processing your request.', sender: 'bot' });
        } finally {
          userInput.value = '';
        }
      }
    };

    return {
      userInput,
      messages,
      sendMessage,
    };
  },
};
</script>

<style>
.chat-container {
  max-width: 400px;
  margin: 0 auto;
}

.chat-messages {
  max-height: 300px;
  overflow-y: auto;
}

.message {
  margin: 5px 0;
}

.user-message {
  text-align: right;
  color: blue;
}

.bot-message {
  text-align: left;
  color: green;
}

.input-container {
  display: flex;
  margin-top: 10px;
}

.input-container input {
  flex: 1;
}

.input-container button {
  margin-left: 5px;
}
</style>