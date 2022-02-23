<template>
  {{ state.keys[Math.floor(Math.random() * 13)] }}<br>
  {{ state.chords[Math.floor(Math.random() * state.chords.length)] }}
</template>

<script>
import {keys, chords} from "../Data.js"
import { reactive } from "vue"

export default {
  name: "Main",
  setup() {
    const state = reactive({
      test: "testing",
      keys: keys,
      chords: chords,
    })
    return {
      state
    }
  }
};
</script>

<style scoped>

</style>
