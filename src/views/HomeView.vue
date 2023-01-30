<template>
  <Login />
  <Main v-if="auth.authStatus === 'authenticated'"></Main>
</template>

<script>
import Login from '@/components/Login.vue';
import Main from '@/components/Main.vue';
import { useAuthenticator } from '@aws-amplify/ui-vue';

export default {
  components: { Login, Main },
  setup() {
    const auth = useAuthenticator();

    return { auth };
  },
  data() {
    return {}
  },
}
</script>
