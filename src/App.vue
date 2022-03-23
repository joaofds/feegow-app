<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          :src="require('@/assets/img/logo.png')"
          transition="scale-transition"
          width="150"
        />
      </div>
      
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
    </v-app-bar>

  <v-sheet height="100%" class="overflow-hidden" style="position: relative;">
    <v-container class="fill-height">
      <v-row align="center" justify="center">
        <v-main>
          <router-view/>
        </v-main>
      </v-row>
    </v-container>

    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-list-item>
        <v-list-item-avatar>
          <v-img :src="require('@/assets/img/user.jpg')"></v-img>
        </v-list-item-avatar>

        <v-list-item-content>
          <v-list-item-title>Jon Doe</v-list-item-title>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list dense>
        <v-list-item
          v-for="item in menuItens" :key="item.title" link>
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
  </v-sheet>

    <v-footer padless>
      <v-col class="text-center" cols="12">
        <strong>jo4o.fds@gmail.com</strong> — {{ new Date().getFullYear() }}
    </v-col>
  </v-footer>
  </v-app>
</template>
<script>
export default {
  name: 'App',

  data() {
    return {
        drawer: null,
        menuItens: [
          { title: 'Home', icon: 'mdi-view-dashboard' },
          { title: 'About', icon: 'mdi-forum' },
        ],
    } 
  },
};
</script>
