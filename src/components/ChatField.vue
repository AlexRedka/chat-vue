<template>
  <v-row
      :style="{
      position: 'absolute',
      bottom: 0,
      left: 0,
      right: 0,
      padding: '10px',
      zIndex: 100,
      background: '#ffffff'
    }"
  >
    <v-col class="py-0" cols="12" md="12">
      <vue-typed-js
          v-if="isChatStarted && isTyping"
          class="typing__wrapper"
          :strings="[typingString]"
          :typeSpeed="50"
          :loop="isTyping"
      >
        <h1 class="typing text-center grey--text font-weight-regular"></h1>
      </vue-typed-js>
      <v-textarea
          v-model="input"
          :error-messages="chatInputErrors"
          :disabled="!isChatStarted || isBtnDisabled"
          filled
          no-resize
          label="Type your message"
      >
        <template #append>
          <v-menu class="emoji__wrapper" offset-y :close-on-content-click="false">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                  color="primary"
                  icon
                  v-bind="attrs"
                  v-on="on"
                  :disabled="!isChatStarted || isBtnDisabled"
              >
                <v-icon class="pointer" @click="isShowEmoji = !isShowEmoji">mdi-sticker-emoji</v-icon>
              </v-btn>
            </template>
            <div>
              <emoji @select="selectEmoji"/>
            </div>
          </v-menu>
        </template>
      </v-textarea>
    </v-col>
    <v-btn
        v-if="isChatStarted"
        class="ml-auto mr-2"
        @click="send"
        x-large
        color="primary"
        :disabled="isBtnDisabled"
    >
      Send Message
    </v-btn>
    <v-btn
        v-else
        class="ml-auto mr-2"
        @click="startChat"
        x-large
        color="primary"
        :disabled="isError"
    >
      Let's chat
    </v-btn>
  </v-row>
</template>

<script>
import Vue from "vue";
import {ref, watch,} from "@vue/composition-api";

import VueTypedJs from "vue-typed-js";
import {Picker} from 'emoji-mart-vue'

Vue.use(VueTypedJs);

export default {
  name: "ChatField",

  model: {
    prop: 'isTyping'
  },

  components: {
    emoji: Picker
  },

  props: {
    currentQuestion: {
      type: Number,
      required: true,
      default: 0
    },
    questions: {
      type: Array,
      required: true,
      default: () => []
    },
    isTyping: Boolean,
    isError: Boolean
  },

  setup(props, {emit}) {
    let input = ref('');
    let chatInputErrors = ref([]);
    let answers = ref({
      name: '',
      age: 0,
      location: '',
      feeling: '',
      hobby: '',
    });
    let isChatStarted = ref(false);
    let isBtnDisabled = ref(false);
    const typingString = ref('Peter typing...');
    const isShowEmoji = false;

    const send = () => {
      if (input.value.length) {
        chatInputErrors.value = []; // reset errors array

        let question = {};
        if (props.questions[props.currentQuestion]) { // if questions exist
          question = props.questions[props.currentQuestion].find(q => q.ask);
          if (question.ask !== undefined) {
            answers.value[question.ask] = input.value;
          }
        }
        emit("next-message", {
          text: input.value,
          ...!!question && {ask: question.ask}, // if question exist - add property "ask"
          owner: "me"
        });

        input.value = "";
      } else {
        chatInputErrors.value.push("This field is required"); // set errors
      }
    };

    const startChat = () => {
      isChatStarted.value = true
      emit('start-chat')
    }

    const selectEmoji = emoji => {
      console.log(emoji)
      input.value += emoji.native;
    };

    watch(() => props.currentQuestion,
        (val) => {
          if (val === props.questions.length - 1) {
            isBtnDisabled.value = true;
          }
        }
    );

    return {
      input,
      chatInputErrors,
      answers,
      isChatStarted,
      isBtnDisabled,
      typingString,
      isShowEmoji,
      send,
      startChat,
      selectEmoji
    }
  },
}
</script>

<style lang="scss">
.typing {
  font-size: 13px;
  line-height: 17px;

  &__wrapper {
    position: absolute;
    top: -12px;
    left: 20px;
    width: 100%;
  }
}
</style>