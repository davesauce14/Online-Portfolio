<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      app
    >
      <v-list dense>
        <v-list-item link :to="'home'">
          <v-list-item-action>
            <v-icon>mdi-home</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item link :to="'resume'">
          <v-list-item-action>
            <v-icon>mdi-file-account</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Digital Resume</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item link :to="'games'">
          <v-list-item-action>
            <v-icon>mdi-gamepad</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Game Library</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item link :to="'renderings'">
          <v-list-item-action>
            <v-icon>mdi-projector-screen</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Renderings</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      app
      color="indigo"
      dark
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container class="fill-height" fluid >
        <router-view></router-view>
      </v-container>
    </v-main>
    <v-footer
      color="indigo"
      app
    >
      <span class="white--text">&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    props: {
      source: String,
    },
    data: () => ({
      drawer: null,
    }),
  }
</script>