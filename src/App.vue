<template>
  <v-app>
    <v-container fluid>
      <v-row
        class="mb-6"
        no-gutters>

        <v-col cols="2" v-if="sidebarShow">
          <Sidebar />
        </v-col>

        <v-col 
          cols="sidebarShow ? 10 : 12" 
          class="pr-3 pl-3">
          <router-view></router-view>
        </v-col>

      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import router from './router';
import Sidebar from './components/Sidebar';

export default {
  name: 'App',
  components : {
    Sidebar
  },
  data: () => ({
  }),

  computed: {
    sidebarShow () {
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
