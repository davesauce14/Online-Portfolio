<template>
    <v-container  >
            <div style="margin: 1.5rem">
                <h1 style="text-align: center">Game Library</h1>
            </div>
            <div>
                <game v-for="game in gamesList" v-bind:key="game.name" v-bind:game="game"></game>
            </div>
    </v-container>
</template>
<script lang="ts">
    import { Component, Vue } from 'vue-property-decorator';
    import Game from '@/views/Game.vue'; // @ is an alias to /src

    @Component({
        components: {
            Game,
        }
    })
    export default class Games extends Vue {
        gamesList: any[] = [];
        
        mounted() {
            fetch('/components.json')
                .then(res => res.json())
                .then(data => {
                   this.gamesList = data; 
                })
        }
    }
</script>