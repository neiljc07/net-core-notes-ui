<template>
  <v-card
    class="mx-auto"
    tile
  >
    <v-list>
      <v-subheader>Menu</v-subheader>
      <v-list-item-group v-model="item" color="primary">
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          @click="goTo(item.path)"
        >
          <v-list-item-icon>
            <v-icon v-text="item.icon"></v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title v-text="item.text"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </v-card>
</template>

<script>
export default {
  data: () => ({
    item: 0,
    items: [
      { text: 'Notes', icon: 'mdi-clock', path : '/' },
      { text: 'Profile', icon: 'mdi-account', path : '/profile' },
      { text: 'Logout', icon: 'mdi-flag', path : '/logout' },
    ],
  }),

  methods: {
    goTo(path) {
      if(path === this.$router.currentRoute.path) {
        return;
      }

      if(path == '/logout') {
        localStorage.removeItem('user');
        this.$router.push('/login');
      } else {
        this.$router.push(path);
      }
    }
  }
};
</script>
