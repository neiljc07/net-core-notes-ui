<template>
  <v-app>
    <router-view></router-view>
  </v-app>
</template>

<script>
import router from './router';

export default {
  name: 'App',
  data: () => ({
  }),

  computed: {
    hide () {
      return ! (this.$route.path === '/login' || this.$route.path === '/register'); 
    }
  },

  methods: {
    logout () {
      localStorage.removeItem('user');
      router.push('/login');
    }
  },
};
</script>
