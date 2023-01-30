<template>
  <v-app :theme="theme">
    <v-app-bar>
      <v-spacer></v-spacer>

      <v-btn :prepend-icon="theme === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night'" @click="onClick">Toggle
        Theme</v-btn>
    </v-app-bar>

    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>
import { ref } from 'vue'

export default {
  name: "App",

  data: () => ({
    theme: ref('light')
  }),
  methods: {
    onClick() {
      this.theme = this.theme === 'light' ? 'dark' : 'light'
    }
  }
};
</script>
