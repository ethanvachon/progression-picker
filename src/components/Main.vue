<template>
  <button @click="randomize()">Randomize</button><br>
  {{ state.randomProgression }}<br>
  {{ state.randomKey }}
  {{ state.randomMode }}
</template>

<script>
import {keys, chords, modes} from "../Data.js"
import { reactive } from "vue"
//import * as Tone from 'tone'

export default {
  name: "Main",
  setup() {
    const state = reactive({
      test: "testing",
      keys: keys,
      chords: chords,
      modes: modes,
      randomProgression: null,
      randomKey: null,
      randomMode: null,
    })
    return {
      state,
      randomize: function () {
        this.state.randomKey = this.state.keys[Math.floor(Math.random() * state.keys.length)]
        this.state.randomProgression = this.state.chords[Math.floor(Math.random() * state.chords.length)]
        this.state.randomMode = this.state.modes[Math.floor(Math.random() * state.modes.length)]
      },
    }
  }
};
</script>

<style scoped>

</style>
