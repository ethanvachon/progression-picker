<template>
  <button @click="randomize()">Randomize</button><br>
  <div v-if="state.ready">
    <p>In the key of {{ state.randomKey.root }} {{ state.randomMode }}</p>
    <div style="display:flex;justify-content:center;">
      <p style="margin:5px" v-for="(chord,i) in state.randomProgression" :key="(chord + i)">{{ chord.label }}</p>
    </div>
    <button @click="playProgression()">Play Progression</button>
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
          notes.push(new this.state.VF.StaveNote({clef: "treble", keys: [n.chord[0][0] +"/4", n.chord[1][0] +"/4", n.chord[2][0] +"/4"], duration: "q" }));
          for(let i = 0;i<3;i++){
            if (n.chord[i][1]) {
              notes[notes.length - 1].addAccidental(i, new this.state.VF.Accidental(n.chord[i][1]))
            }
          }
        })
        var voice = new this.state.VF.Voice({num_beats: 4,  beat_value: 4});
        voice.addTickables(notes);
        new this.state.VF.Formatter().joinVoices([voice]).format([voice], 350);
        voice.draw(this.state.context, this.state.stave);
      },
      determineMinMaj: function (key) {
        let keyInScale
        let toPush
        let fullScale
        switch(key) {
          case 1:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Maj/Ionian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 2:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Min/Dorian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 3:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Min/Phrygian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 4:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Maj/Lydian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 5:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Maj/Mixolydian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 6:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Min/Aeolian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
            break;
          case 7:
            keyInScale = this.state.randomKey.scale[(key-1)]
            fullScale = this.state.keys.find(k => k.root == keyInScale).scale
            toPush = {
              label: keyInScale + " Dim/Locrian",
              chord: [this.state.randomKey.scale.find(n => n[0] == fullScale[0][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[2][0]), this.state.randomKey.scale.find(n => n[0] == fullScale[4][0])],
            }
            this.state.randomProgression.push(toPush)
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
        renderer.resize(500, 150);
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
      playProgression: function () {
        console.log("Playing sound!")
      },
    }
  }
};
</script>

<style scoped>

</style>
