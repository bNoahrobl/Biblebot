<template>
  <div id="app">
    <div class="chat-container">
      <div class="chat-messages">
        <div v-for="(message, index) in messages" :key="index" class="message">
          <div v-if="message.sender === 'user'" class="user-message">
            <div class="message-container">
              <p class="message-text">{{ message.text }}</p>
              <div class="user-details">
                <img src="@/assets/User.jpeg" alt="User" class="profile-picture" />
                <p class="name">{{ message.name }}</p>
              </div>
            </div>
          </div>
          <div v-else class="bot-message">
            <div class="message-container">
              <p class="message-text">{{ message.text }}</p>
              <div class="user-details">
                <img src="@/assets/logo.jpg" alt="BibleBot" class="profile-picture" />
                <p class="name">{{ message.name }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="input-container">
        <input v-model="userInput" @keyup.enter="sendMessage" type="text" placeholder="Send a message" />
      
        <button @click="sendMessage"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" class="text-white dark:text-black"><path d="M7 11L12 6L17 11M12 18V7" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"></path></svg></button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';


export default {
  
  name: 'App',
  setup() {
    const userInput = ref('');
    const messages = ref([]);
    onMounted(()=>{
      if(messages.value.length===0){
        messages.value.push({ text: 'Hallo ich bin der BibelBot, stelle mir deine Fragen', sender: 'bot', name: 'BibleBot' });
      }
    });
    const sendMessage = async () => {
  const userMessage = userInput.value.trim();
  
  if (userMessage !== '') {
    let modifiedUserMessage = userMessage;
    if (!userMessage.startsWith('Antworte nur mit biblischen versen oder biblischen ratschlägen:')) {
      modifiedUserMessage = 'Antworte mit einem biblischen vers und einem christlichen ratschlag. je nach dem was besser passt:' + userMessage;
    }
    
    messages.value.push({ text: userMessage, sender: 'user', name: 'You' });
    try {
      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          // Replace with your valid GPT-4 API key
          Authorization: 'Bearer InsertKey',
        },
        body: JSON.stringify({ model: 'gpt-3.5-turbo', messages: [{ role: 'user', content: modifiedUserMessage }] }), 
      });
      
      if (!response.ok) {
        throw new Error('Failed to fetch response from GPT-4 API');
      }
      
      const data = await response.json();
      const botResponse = data.choices[0].message.content;
      messages.value.push({ text: botResponse, sender: 'bot', name: 'BibleBot' });
    } catch (error) {
      console.error('Error fetching response:', error);
      messages.value.push({ text: 'An error occurred while processing your request.', sender: 'bot', name: 'BibleBot' });
    } finally {
      userInput.value = '';
    }
  }
};

    onMounted(async () => {
      // Send initial message from the bot when the component is mounted
      if (messages.value.length === 0) {
        await sendMessage();
      }
    });

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
  width:auto;
  max-width: 80%;
  margin: 0 auto;
  
}

.chat-messages {
  max-height: calc(100vh - 100px); /* Adjusted to accommodate the fixed input container */
  overflow-y: auto;
}

.message {
  margin: 10px 0;
  display: flex;
  align-items: flex-end;
}

.user-message {
  align-self: flex-end;
  margin-left: auto;
}

.bot-message {
  align-self: flex-start;
  margin-right: auto;
}

.message-container {
  position: relative;
}

.message-text {
  background-color: #f3f4f6;
  padding: 10px;
  border-radius: 10px;
  max-width: 70%;
}

.user-details {
  display: flex;
  align-items: center;
  position: absolute;
  top: -20px;
}

.profile-picture {
  width: 30px;
 
  border-radius: 50%;
  margin-right: 5px;
}

.name {
  font-weight: bold;
  margin: 0;
  color: #333;
}

.input-container {
  position: fixed;
  bottom: 30px;
  border-radius: 10px;
  border-color:#333;
  border:black;
  text-align:center;
  
  width: 80%;
  background-color: #f9f9f9;
  padding: 10px;
  display: flex;
  align-items: center;
}

.input-container input {
  flex: 1;
  font-size:x-large;
  margin-right: 10px;
  border:#fff;

  background-color: #f9f9f9 ;
}


.input-container button {
  background-color: #007bff;
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}
</style>