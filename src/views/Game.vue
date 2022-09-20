<template>
  <div style="display: inline-block; ">
    <v-card class="mx-auto" width="350" style="display: inline-block; margin: 1rem !important">
      <v-img class="white--text align-end" height="200px" :src="game.pictureUrl">
        <v-card-title style="color: white;
  mix-blend-mode: unset;">{{ game.name }}</v-card-title>
      </v-img>

      <v-card-subtitle class="pb-0">{{ game.name }}</v-card-subtitle>

      <v-card-text class="text--primary">
        <div>Whitehaven Beach</div>

        <div>Whitsunday Island, Whitsunday Islands</div>
      </v-card-text>

      <v-card-actions>
        <v-btn @click="dialog = true">Play</v-btn>

        <v-btn outlined>Info</v-btn>
        <div style="width: 100%">
          <v-icon class="float-right" v-for="language in game.technologies" v-bind:key="language">mdi-{{ language.icon }}</v-icon>
        </div>
      </v-card-actions>
    </v-card>
    <v-dialog v-model="dialog" fullscreen hide-overlay transition="dialog-bottom-transition">
      <v-card>
        <v-toolbar dark color="primary">
          <v-btn icon dark @click="dialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>{{ game.name }}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn dark text @click="dialog = false">Back to dem Games</v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <p>{{ game.description }}</p>
        <v-divider></v-divider>
        <iframe class="big-iframe" :src="game.artifactUrl" frameborder="0"></iframe>
      </v-card>
    </v-dialog>
  </div>
</template>
<script lang="ts">
import { Vue, Component, Prop } from "vue-property-decorator";

@Component({})
export default class Game extends Vue {
  @Prop() game?: any[];
  dialog = false;
  mounted() {
    console.log(this.game);
  }
}
</script>