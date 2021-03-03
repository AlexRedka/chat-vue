<template>
  <v-row class="chat-messages__wrapper">
    <v-col cols="12" class="d-flex align-center justify-center fill-height pa-0" v-if="!isChatStarted">
      <p class="text-center grey--text">
        Click the button "Let's chat" to start chatting
      </p>
    </v-col>
    <ul v-else>
      <li v-for="(message, index) in messages"
          :key="index"
          :class="message.owner"
      >
        {{ message.text }}
      </li>
    </ul>
  </v-row>
</template>

<script>

export default {
  name: "ChatMessages",

  props: {
    messages: {
      type: Array,
      required: true,
      default: () => []
    },
    isChatStarted: {
      type: Boolean,
      required: true,
      default: false
    }
  },

  updated() {
    if (this.$el.querySelector('ul')) {
      this.$el.scrollTop = this.$el.querySelector('ul').clientHeight;
    }
  }
}
</script>

<style scoped lang="scss">
.chat-messages__wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 390px;
  overflow-y: scroll;

  ul {
    width: 100%;
    margin: 0;
    padding-top: 10px;

    li {
      display: inline-block;
      clear: both;
      padding: 20px;
      border-radius: 30px;
      margin-bottom: 2px;
      font-family: Helvetica, Arial, sans-serif;

      &.him {
        background: #eee;
        float: left;
      }

      &.me {
        float: right;
        background: #0084ff;
        color: #fff;

        &:last-of-type {
          border-bottom-right-radius: 30px;
        }
      }
    }

    .him + .me {
      border-bottom-right-radius: 5px;
    }

    .me + .me {
      border-top-right-radius: 5px;
      border-bottom-right-radius: 5px;
    }
  }
}
</style>