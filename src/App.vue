<template>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="monsterBarStyle"></div>
      </div>
    </section>
    <section id="hero" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="heroBarStyle"></div>
      </div>
    </section>
    <section class="container" v-if="winner">
      <h2>Game Over!</h2>
      <h3>{{ winner }}</h3>
      <button @click="retryGame">Retry Game</button>
    </section>
    <section id="controls" v-else>
      <button @click="monsterAttack">ATTACK</button>
      <button @click="magicAttack" :disabled="magicAbility">MAGIC ATTACK</button>
      <button @click="heroHeal" :disabled="healAbility">HEAL</button>
      <button @click="surrender">SURRENDER</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul></ul>
    </section>
  </div>
</template>

<script>
function getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min)) + min
}

export default {
  data() {
    return {
      heroHealth: 100,
      monsterHealth: 100,
      currentRound: 0,
      winner: null
    }
  },
  methods: {
    heroAttack() {
      const attackValue = getRandomValue(8, 15)
      this.heroHealth -= attackValue
    },
    monsterAttack() {
      this.currentRound++
      const attackValue = getRandomValue(5, 12)
      this.monsterHealth -= attackValue
      this.heroAttack()
    },
    magicAttack() {
      this.currentRound++
      const attackValue = getRandomValue(13, 20)
      this.monsterHealth -= attackValue
      this.heroAttack()
    },
    heroHeal() {
      this.currentRound++
      const healValue = getRandomValue(12, 25)
      if (this.heroHealth + healValue > 100)
        this.heroHealth = 100
      else
        this.heroHealth += healValue
      this.heroAttack()
    },
    surrender() {
      this.heroHealth = 0
    },
    retryGame() {
      this.heroHealth = 100
      this.monsterHealth = 100
      this.currentRound = 0
      this.winner = null
    }
  },
  computed: {
    heroBarStyle() {
      return {width: this.heroHealth + '%'}
    },
    monsterBarStyle() {
      return {width: this.monsterHealth + '%'}
    },
    magicAbility() {
      return this.currentRound % 3
    },
    healAbility() {
      return !(this.currentRound % 3 && this.heroHealth < 50);
    }
  },
  watch: {
    heroHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0)
        this.winner = 'A draw'
      else if (value <= 0)
        this.winner = 'The Hero is dead! Monsters destroyed the village...'
    },
    monsterHealth(value) {
      if (value <= 0 && this.heroHealth <= 0)
        this.winner = 'A draw'
      else if (value <= 0)
        this.winner = 'No monster survived... The Hero won!'
    }
  }
}
</script>

<style scoped>
* {
  box-sizing: border-box;
}

html {
  font-family: 'Jost', sans-serif;
}

body {
  margin: 0;
}

header {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 0.5rem;
  background-color: #880017;
  color: white;
  text-align: center;
  margin-bottom: 2rem;
}

section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: #fde5e5;
}

.healthbar__value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}

#monster h2,
#hero h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #88005b;
  background-color: #88005b;
  color: white;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 12rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--hero {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>
