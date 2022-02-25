<template>
  <button @click="randomize()">Randomize</button><br>
  <div v-if="state.ready">
    <p>In the key of {{ state.randomKey.root }} {{ state.randomMode }}</p>
    <div style="display:flex;justify-content:center;">
      <p style="margin:5px" v-for="(chord,i) in state.randomProgression" :key="(chord + i)">{{ chord }}</p>
    </div>
  </div>
</template>

<script>
import {keys, chords, modes} from "../Data.js"
import { reactive } from "vue"
//import * as Tone from 'tone'

export default {
  name: "Main",
  setup() {
    const state = reactive({
      ready: false,
      keys: keys,
      chords: chords,
      modes: modes,
      unprocessedProgression: null,
      randomProgression: [],
      randomKey: null,
      randomMode: null,
    })
    return {
      state,
      randomize: function () {
        this.reset()

        this.state.randomKey = this.state.keys[Math.floor(Math.random() * state.keys.length)]

        this.state.unprocessedProgression = this.state.chords[Math.floor(Math.random() * state.chords.length)]

        for(let i=0;i<this.state.unprocessedProgression.length;i++) {
          this.determineMinMaj(this.state.unprocessedProgression[i], i)
        }

        this.state.randomMode = this.state.modes[Math.floor(Math.random() * state.modes.length)]

        this.state.ready = true
      },
      determineMinMaj: function (key) {
        let keyInScale
        switch(key) {
          case 1:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Maj/Ionian")
            break;
          case 2:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Min/Dorian")
            break;
          case 3:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Min/Phrygian")
            break;
          case 4:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Maj/Lydian")
            break;
          case 5:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Maj/Mixolydian")
            break;
          case 6:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Min/Aeolian")
            break;
          case 7:
            keyInScale = this.state.randomKey.scale[(key-1)]
            this.state.randomProgression.push(keyInScale + " Dim/Locrian")
            break;
        }
        // let keyInScale = this.state.randomKey.scale[(key-1)]
        // if(keyInScale.includes("#")) {
        //   keyInScale = this.state.randomKey.scale[key][0] + "b"
        // }
        // let progressionKey = this.state.keys.find(k => k.root == keyInScale)
        // if(this.state.randomKey.scale.includes(progressionKey.scale[2])) {
        //   this.state.randomProgression.push(keyInScale + " Major")
        // } else {
        //   this.state.randomProgression.push(keyInScale + " Minor")
        // }
        return
      },
      reset: function () {
        this.state.unprocessedProgression = null
        this.state.randomProgression = []
        this.state.randomKey = null
        this.state.randomMode = null
      },
    }
  }
};
</script>

<style scoped>

</style>
