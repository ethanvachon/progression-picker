<template>
  <button @click="randomize()">Randomize</button><br>
  <div v-if="state.ready">
    <p>In the key of {{ state.randomKey.root }} {{ state.randomMode }}</p>
    <div style="display:flex;justify-content:center;">
      <p style="margin:5px" v-for="(chord,i) in state.randomProgression" :key="(chord + i)">{{ chord }}</p>
    </div>
  </div>
  <div id="staff"></div>
</template>

<script>
import {keys, chords, modes} from "../Data.js"
import { reactive, onMounted } from "vue"
//import * as Tone from 'tone'
import Vex from 'vexflow';

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

      VF: Vex.Flow,
      context: null,
      stave: null,
    })
    onMounted(async() => {
        
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
        var notes = [];
        this.state.randomProgression.forEach(n => {
          notes.push(new this.state.VF.StaveNote({clef: "treble", keys: [n[0] +"/4"], duration: "q" }));
          if(n[1] != " ") {
            notes[notes.length - 1].addAccidental(0, new this.state.VF.Accidental(n[1]))
          }
        })
        var voice = new this.state.VF.Voice({num_beats: 4,  beat_value: 4});
        voice.addTickables(notes);
        new this.state.VF.Formatter().joinVoices([voice]).format([voice], 350);
        voice.draw(this.state.context, this.state.stave);
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
        return
      },
      reset: function () {
        const staff = document.getElementById('staff');
        while (staff.hasChildNodes()) {
          staff.removeChild(staff.lastChild);
        }

        var div = document.getElementById("staff")
        var renderer = new this.state.VF.Renderer(div, this.state.VF.Renderer.Backends.SVG);  
        renderer.resize(500, 500);
        this.state.context = renderer.getContext();
        this.state.context.setFont("Arial", 10, "").setBackgroundFillStyle("#eed");
        this.state.stave = new this.state.VF.Stave(10, 40, 400);
        this.state.stave.addClef("treble").addTimeSignature("4/4");
        this.state.stave.setContext(this.state.context).draw();

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
