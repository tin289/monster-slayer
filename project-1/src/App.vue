<template>
  <div class="container-page">
    <link
      href="https://fonts.googleapis.com/css2?family=Jost:wght@400;700&display=swap"
      rel="stylesheet"
    />

    <header>
      <h1>Monster Slayer</h1>
    </header>

    <div id="log-in" v-if="show">
      <section class="container">
        <input
          type="text"
          placeholder="Pls enter your name"
          v-model="name"
          @keydown.enter="startGame"
        />
        <button @click="startGame">Start</button>
      </section>
    </div>

    <div id="game" v-else>
      <section id="monster" class="container">
        <h2>Monster Health</h2>
        <div class="healthbar">
          <div class="healthbar_value" :style="monsterHealthBar">
            {{ monsterHealth }} HP
          </div>
        </div>
      </section>

      <section id="player" class="container">
        <h2>{{ name }} Health</h2>
        <div class="healthbar">
          <div class="healthbar_value" :style="playerHealthBar">
            {{ playerHealth }} HP
          </div>
        </div>
      </section>

      <section class="container" v-if="winner">
        <h2>Game Over</h2>
        <h3 v-if="winner === 'lost'">You lost</h3>
        <h3 v-else-if="winner === 'win'">You win</h3>
        <h3 v-else>Draw</h3>
        <button @click="newGame">New Game</button>
      </section>

      <section id="controls" v-else>
        <button @click="playerAttack">ATTACK</button>
        <button @click="specialAttack" :disabled="timeUseSpecialAttack">
          SPECIAL ATTACK
        </button>
        <button @click="healing">HEAL</button>
        <button @click="surrender">SURRENDER</button>
      </section>

      <section id="log" class="container log">
        <h2>Battle Log</h2>
        <ul>
          <li v-for="battleLog in battleLogs" :key="battleLog">
            <span
              :class="{
                'log--player': battleLog.actionBy === 'player',
                'log--monster': battleLog.actionBy === 'monster',
              }"
              >{{ battleLog.actionBy === "player" ? name : "Monster" }}</span
            >
            <span v-if="battleLog.actionType === 'heal'">
              heals
              <span class="log--heal">{{ battleLog.actionValue }} HP</span>
            </span>
            <span v-else-if="battleLog.actionType === 'attack'">
              attacks and deals
              <span class="log--damage">{{ battleLog.actionValue }} HP</span>
            </span>
            <span v-else>
              uses special attack and deals
              <span class="log--damage">{{ battleLog.actionValue }} HP</span>
            </span>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: "project-1",
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      currentRound: 0,
      winner: null,
      battleLogs: [],
      name: "",
      show: true,
    };
  },

  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "lost";
      }
    },

    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "win";
      }
    },
  },

  methods: {
    playerAttack() {
      const playerDamage = Math.floor(Math.random() * (8 - 5) + 5);
      this.monsterHealth = this.monsterHealth - playerDamage;
      this.monsterAttack();
      this.currentRound++;
      this.logMessage("player", "attack", playerDamage);
    },

    monsterAttack() {
      const monsterDamage = Math.floor(Math.random() * (10 - 7) + 7);
      this.playerHealth = this.playerHealth - monsterDamage;
      this.logMessage("monster", "attack", monsterDamage);
    },

    specialAttack() {
      const specialDamage = Math.floor(Math.random() * (16 - 10) + 10);
      this.monsterHealth = this.monsterHealth - specialDamage;
      this.logMessage("player", "special attack", specialDamage);
      this.monsterAttack();
      this.currentRound = 0;
    },

    healing() {
      const heal = Math.floor(Math.random() * (12 - 7) + 7);
      if (this.playerHealth + heal > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth = this.playerHealth + heal;
      }
      this.logMessage("player", "heal", heal);
      this.monsterAttack();
      this.currentRound++;
    },

    surrender() {
      this.winner = "lost";
    },

    newGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.currentRound = 0;
      this.winner = null;
      this.battleLogs = [];
    },

    logMessage(who, what, value) {
      this.battleLogs.unshift({
        actionBy: who,
        actionType: what,
        actionValue: value,
      });
    },

    startGame() {
      this.show = !this.show;
    },
  },

  computed: {
    timeUseSpecialAttack() {
      return this.currentRound < 3;
    },

    playerHealthBar() {
      if (this.playerHealth < 0) {
        return { width: "0" };
      }
      return { width: this.playerHealth + "%" };
    },

    monsterHealthBar() {
      if (this.monsterHealth < 0) {
        return { width: "0" };
      }
      return { width: this.monsterHealth + "%" };
    },
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: "Jost", sans-serif;
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

.healthbar_value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}

#monster h2,
#player h2 {
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
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;
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

.log--player {
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

input {
  font: inherit;
  border: 1px solid #ccc;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 20rem;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

input:focus {
  outline: none;
  border-color: #e696dd;
  background-color: pink;
}

.log {
  background: rgb(246, 188, 198);
}
</style>
