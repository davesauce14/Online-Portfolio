<template>
    <v-container fluid class="big-iframe">
      <v-row class="mb-6 big-iframe" no-gutters>
        <v-col sm="12" lg="6">
          <h1>Dave Gould</h1>
          <h3 class="text--secondary">Software Developer</h3>
        </v-col>
        <v-col sm="12" lg="6">
          <v-img 
            src="/nobg.png"

            class="shadow"
            width="90%">
          </v-img>
        </v-col>
      </v-row>
    </v-container>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src

@Component({
  components: {
    HelloWorld,
  },
})
export default class Home extends Vue {}
</script>
