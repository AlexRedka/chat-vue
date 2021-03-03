<template>
  <v-app>
    <v-app-bar
        app
        color="primary"
        dark
    >
      <div class="d-flex align-center">

      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <chat />
    </v-content>
  </v-app>
</template>

<script>

import Chat from "@/components/Chat";
export default {
  name: 'App',

  components: { Chat }
};
</script>

<style>
</style>
