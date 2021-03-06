<template>
  <div class="d-flex flex-column align-items-center mt-4">
    <div class="d-flex justify-content-center">
      <button class="btn-sm btn-primary mx-2" @click="randomize()">Randomize</button><br>
      <button class="btn-sm btn-primary mx-2" @click="playProgression()" v-if="state.ready">Play Progression</button>
    </div>
    
    <div class="card border-2 mt-3" v-if="state.ready">
      <h5 class="card-header m-0">{{ state.randomKey.root }} {{ state.randomMode }}</h5>
      <div class="d-flex justify-content-center">
        <div class="text-center" v-for="(chord,i) in state.randomProgression" :key="(chord + i)">
          <p class="m-0 fw-bold">{{chord.chord[0]}}</p>
          <h6 class="mx-3">{{ toRomanNumeral(state.unprocessedProgression[i]) }}</h6>
        </div>
      </div>
    </div>

    <div id="staff"></div>
  </div>
</template>

<script>
import {keys, chords, modes, numerals} from "../Data.js"
import { reactive, onMounted } from "vue"
import * as Tone from 'tone'
import Vex from 'vexflow';

export default {
  name: "Main",
  setup() {
    const state = reactive({
      ready: false,

      keys: keys,
      chords: chords,
      modes: modes,
      numerals: numerals,
      
      unprocessedProgression: null,
      randomProgression: [],
      randomKey: null,
      randomMode: null,

      progressionChords: [],
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
        this.state.randomMode = this.state.modes[Math.floor(Math.random() * state.modes.length)]
        
        if(this.state.randomMode == "Minor") {
          this.state.randomKey.minorScale = JSON.parse(JSON.stringify(this.state.randomKey.scale));
          for (let i = 0; i <= 7; i++) {
            if(i == 2 || i == 5 || i == 6) {
              if(this.state.randomKey.minorScale[i].length > 1 && this.state.randomKey.minorScale[i][1] == "#") {
                this.state.randomKey.minorScale[i] = this.state.randomKey.minorScale[i][0]
              } else if(this.state.randomKey.minorScale[i].length > 1 && this.state.randomKey.minorScale[i][1] == "b" && this.state.randomKey.minorScale[i - 1][0] != this.state.randomKey.minorScale[i - 1]) {
                this.state.randomKey.minorScale[i] = this.state.randomKey.minorScale[i - 1][0]
              } else if(this.state.randomKey.minorScale[i].length == 1) {
                this.state.randomKey.minorScale[i] += 'b'
              }
            }
          }
        }
        for(let i=0;i<this.state.unprocessedProgression.length;i++) {
          this.determineMinMaj(this.state.unprocessedProgression[i], i)
        }
        this.state.ready = true
        var notes = [];
        this.state.randomProgression.forEach(n => {
          notes.push(new this.state.VF.StaveNote({clef: "treble", keys: [n.chord[0][0] +"/4", n.chord[1][0] +`/${n.chord[0][0] == "A" || n.chord[0][0] == "Bb" || n.chord[0][0] == "B" ? 5 : 4}`, n.chord[2][0] +`/${n.chord[0][0] == "F" || n.chord[0][0] == "Gb" || n.chord[0][0] == "G" || n.chord[0][0] == "Ab" || n.chord[0][0] == "A" || n.chord[0][0] == "Bb" || n.chord[0][0] == "B" ? 5 : 4}`], duration: "q" }));
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
        let scale = this.state.randomMode == "Major" ? this.state.randomKey.scale : this.state.randomKey.minorScale
        let keyInScale = scale[(key-1)]
        console.log(keyInScale)
        let fullScale = this.state.keys.find(k => k.root == keyInScale)
        if(!fullScale) {
          let baseScale = this.state.keys.find(k => k.root == "C")
          let alternateNote
          if(keyInScale.includes('b')) {
            alternateNote = (baseScale.scale[(baseScale.scale.indexOf(keyInScale[0]) - 1)])
          } else {
            alternateNote = (baseScale.scale[(baseScale.scale.indexOf(keyInScale[0]) + 1)] + "b")
          }
          fullScale = this.state.keys.find(k => k.root == alternateNote)
        }
        fullScale = fullScale.scale
        console.log(fullScale)
        console.log(scale)
        let chord = [scale.find(n => n[0] == fullScale[0][0]), scale.find(n => n[0] == fullScale[2][0]), scale.find(n => n[0] == fullScale[4][0])]
        this.state.progressionChords.push(chord)
        this.state.randomProgression.push({
          chord: chord
        })
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

        this.state.progressionChords = []
        this.state.unprocessedProgression = null
        this.state.randomProgression = []
        this.state.randomKey = null
        this.state.randomMode = null
      },
      toRomanNumeral: function(number) {
        return this.state.numerals.find(n => n.mode == this.state.randomMode).numerals[number - 1]
      },
      playProgression: function () {
        const synth = new Tone.Synth().toDestination();
        const now = Tone.now()

        synth.triggerAttackRelease(`${state.randomProgression[0].chord[0]}4`, "8n", now)
        synth.triggerAttackRelease(`${state.randomProgression[1].chord[0]}4`, "8n", now + 1)
        synth.triggerAttackRelease(`${state.randomProgression[2].chord[0]}4`, "8n", now + 2)
        synth.triggerAttackRelease(`${state.randomProgression[3].chord[0]}4`, "8n", now + 3)
      },
    }
  }
};
</script>

<style scoped>
.card {
  width: fit-content;
}
</style>
