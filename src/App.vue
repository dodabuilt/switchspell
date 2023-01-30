<template>
  <v-app :theme="theme">
    <v-app-bar>
      <v-app-bar-title>Spell Switch</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-btn :prepend-icon="theme === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night'" @click="toggleTheme"></v-btn>
      <v-menu>
        <template v-if="auth.authStatus === 'authenticated'" v-slot:activator="{ props }">
          <v-btn icon="mdi-account" v-bind="props"></v-btn>
        </template>

        <v-list v-model:opened="open">
          <v-list-item prepend-icon="mdi-account" title="Profile"></v-list-item>
          <v-list-item prepend-icon="mdi-logout" title="Logout" @click="auth.signOut"></v-list-item>
        </v-list>
      </v-menu>

    </v-app-bar>
    <v-main>
      <v-container>
        <router-view />
      </v-container>

    </v-main>
  </v-app>
</template>

<script>
import { ref, toRefs } from 'vue'
import { useAuthenticator } from '@aws-amplify/ui-vue';

export default {
  name: "App",
  setup() {
    const auth = useAuthenticator();

    return { auth };
  },
  data: () => ({
    username: null,
    isLoggedIn: null,
    theme: ref('dark'),
    open: ['Users'],
    admins: [
      ['Management', 'mdi-account-multiple-outline'],
      ['Settings', 'mdi-cog-outline'],
    ],
    cruds: [
      ['Create', 'mdi-plus-outline'],
      ['Read', 'mdi-file-outline'],
      ['Update', 'mdi-update'],
      ['Delete', 'mdi-delete'],
    ],
  }),

  methods: {
    toggleTheme() {
      this.theme = this.theme === 'light' ? 'dark' : 'light'
      console.log(this.auth.user)
    }
  }
};
</script>
