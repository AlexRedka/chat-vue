<template>
  <v-container fluid>
    <chat-messages
        :current-question="currentQuestion"
        :messages="messages"
        :is-chat-started="isChatStarted"
    />
    <chat-field
        v-model="isTyping"
        :current-question="currentQuestion"
        :questions="questions"
        :is-error="isError"
        @next-message="nextMessage"
        @start-chat="startChat"
    />
  </v-container>
</template>

<script>
import axios from 'axios';
import { delay } from "@/helpers";
import { ref } from "@vue/composition-api";

import ChatField from "@/components/ChatField";
import ChatMessages from "@/components/ChatMessages";

export default {
  name: "Chat",
  components: {ChatMessages, ChatField},
  setup() {
    let currentQuestion = ref(0);
    let messages = ref([]);
    let questions = ref([]);
    let isChatStarted = ref(false);
    let robotTypingTime = ref(1500);
    let isTyping = ref(false);
    let isError = ref(false);

    const nextMessage = message => {
      isTyping.value = true
      messages.value.push(message);
      ++currentQuestion.value;
      delay(robotTypingTime.value)
        .then(() => {
          questions.value[currentQuestion.value] && messages.value.push(...questions.value[currentQuestion.value]);
          isTyping.value = false
        });
    };

    const startChat = () => {
      isChatStarted.value = true;
      isTyping.value = true;
      delay(robotTypingTime.value)
        .then(() => {
          messages.value = [...questions.value[currentQuestion.value]];
          isTyping.value = false;
        });
    };

    const getQuestions = () => {
      axios
      .get('questions.json')
      .then(res => questions.value = res.data)
      .catch(() => isError.value = true);
    };

    getQuestions();

    return {
      currentQuestion, messages, questions, isChatStarted, robotTypingTime, isTyping, isError, nextMessage, startChat,
    }
  },
}
</script>

<style>
</style>