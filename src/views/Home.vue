<template>
  <section class="container">
    <div class="rpgui-pixelated">
      <div>
        <h1 class="title">Kill slime</h1>
      </div>
      <div class="panel">
        <div class="scores">
          <div class="score">
            <h1>Player</h1>
            <img src="./assets/gifs/sprite-1.gif" alt="Sprite 1">
            <div class="life-bar rpgui-progress rpgui-progress-track">
              <div 
                class="rpgui-progress-fill green"
                :class="{ danger: playerLife < 20 }"
                :style="{ width: playerLife + '%'}"
              >
              </div>
            </div>
            <p>{{ playerLife }}%</p>
          </div>
          <div class="score">
            <h1>Slime</h1>
            <img src="./assets/gifs/sprite-2.gif" alt="Sprite 2">
            <div class="life-bar rpgui-progress rpgui-progress-track">
              <div 
                class="rpgui-progress-fill red"
                :class="{ danger: slimeLife < 20 }"
                :style="{ width: slimeLife + '%'}"
              >
              </div>
            </div>
            <p>{{ slimeLife }}%</p>
          </div>
        </div>
        <div class="rpgui-container framed-golden buttons">
          <template v-if="running">
            <div class="btn">
              <button class="btn attack rpgui-icon sword" @click="attack(false)"></button>
              <p>Attack</p>
            </div>
            <div class="btn">
              <button class="btn especial-attack rpgui-icon sword" @click="attack(true)"></button>
              <p>Especial attack</p>
            </div>
            <div class="btn">
              <button class="rpgui-icon potion-red" @click="healPlayer()"></button>
              <p>Heal</p>
            </div>
            <div class="btn">
              <button @click="running = false" class="btn rpgui-icon shield"></button>
              <p>Give up / Play again</p>
            </div>
          </template>
          <button v-else @click="startGame" class="rpgui-button">Play!</button>
        </div>
      </div>
      <div v-if="hasResult" class="panel result">
        <h3 v-if="slimeLife == 0" class="win">You won :)</h3>
        <h3 v-else class="lose">You lose :'(</h3>
      </div>
      <div v-if="logs.length" class="panel logs">
        <ul>
          <li :v-for="log in logs" :class="log.cls" class="log">{{ log.text }}</li>
        </ul>
      </div>
    </div>
  </section>
</template>

<script> 
export default {
  data () {
    return {
      running: false,
      playerLife: 100,
      slimeLife: 100,
      logs: []
    }
  },
  computed: {
    hasResult() {
      return this.playerLife == 0 || this.slimeLife == 0
    }
  },
  methods: {
    startGame() {
      this.running = true
      this.playerLife = 100
      this.slimeLife = 100
      this.log = []
    },
    attack(especial) {
      this.hurt('slimeLife', 5, 10, especial, 'Player', 'Slime', 'player')
      if(this.slimeLife > 0) {
        this.hurt('playerLife', 7, 12, false, 'Slime', 'Player', 'slime')
      }
    },
    hurt(prop, min, max, especial, source, target, cls) {
      const plus = especial ? 5 : 0
      const hurt = this.getRandom(min + plus, max + plus)
      this[prop] = Math.max(this[prop] - hurt, 0)
      this.registerLog(`${source} attacks ${target} with ${hurt}`, cls)
    },
    healPlayer() {
      this.heal(10, 15)
    },
    heal(min, max) {
      const heal = this.getRandom(min, max)
      this.playerLife = Math.min(this.playerLife + heal, 100)
      this.registerLog(`The player received ${heal} of heal!`, 'player')
    },
    getRandom(min, max) {
      const value = Math.random() * (max - min) + min
      return Math.round(value)
    },
    registerLog(text, cls) {
      this.logs.unshift({ text, cls })
    }
  },
  watch: {
    wasResult(value) {
      if (value) this.running = false
    }
  }
}
</script>

<style lang="scss">
@import './assets/scss/style.scss';
@import './assets/css/rpgui.css';
</style>