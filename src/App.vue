<template>
  <div>
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            v-bind:style="{ width: hp + '%' }"
          ></div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">M</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            v-bind:style="{ width: monsterHp + '%' }"
          ></div>
        </div>
      </div>
    </section>
    <section class="row controls">
      <div class="small-12 columns">
        <button id="start-game" @click="startNewGame()">START NEW GAME</button>
      </div>
    </section>
    <section class="row controls">
      <div class="small-12 columns" v-if="!result">
        <button
          id="attack"
          @click="
            doMove('you');
            doMove('monster');
          "
        >
          RANDOM
        </button>
        <button
          id="attack"
          @click="
            doMove('you', 'attack');
            doMove('monster');
          "
        >
          ATTACK
        </button>
        <button
          id="special-attack"
          @click="
            doMove('you', 'specialAttack');
            doMove('monster');
          "
        >
          SPECIAL ATTACK
        </button>
        <button
          id="heal"
          @click="
            doMove('you', 'heal');
            doMove('monster');
          "
        >
          HEAL
        </button>
        <button id="give-up" @click="giveUp()">
          GIVE UP
        </button>
      </div>
      <div v-else class="small-12 columns">
        <p>{{ result }}</p>
      </div>
    </section>
    <section class="row log">
      <div class="small-12 columns">
        <ul>
          <li
            v-for="(move, index) in log"
            v-bind:key="index"
            v-bind:class="move.turnBy == 'you' ? 'player-turn' : 'monster-turn'"
          >
            {{ move.turnBy }} uses {{ move.name }}! {{ move.message }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "app",
  data: function() {
    return {
      hp: 100,
      monsterHp: 100,
      result: null,
      moves: {
        attack: {
          name: "Attack",
          strength: 10,
          recovery: 0
        },
        specialAttack: {
          name: "Special Attack",
          strength: 15,
          recovery: 0
        },
        heal: {
          name: "Heal",
          strength: 0,
          recovery: 10
        }
      },
      log: []
    };
  },
  computed: {
    hps() {
      return {
        hp: this.hp,
        monsterHp: this.monsterHp
      };
    }
  },
  methods: {
    startNewGame() {
      this.monsterHp = 100;
      this.hp = 100;
      this.log = [];
      this.result = null;
    },
    giveUp() {
      if (confirm("Do you want to start over?")) {
        this.startNewGame();
      }
    },
    doMove(turnBy = "you", type = "random") {
      let move;
      if (type == "random") {
        const moveKeys = Object.keys(this.moves);
        const moveIndex = Math.floor(Math.random() * moveKeys.length);
        const randomMovekey = moveKeys[moveIndex];
        move = this.moves[randomMovekey];
      } else {
        move = this.moves[type];
      }

      if (turnBy === "you") {
        this.monsterHp = Math.max(this.monsterHp - move.strength, 0);
        this.hp = Math.min(this.hp + move.recovery, 100);
      } else if (turnBy === "monster") {
        this.hp = Math.max(this.hp - move.strength, 0);
        this.monsterHp = Math.min(this.monsterHp + move.recovery, 100);
      }
      this.log.push({
        name: move.name,
        turnBy,
        message: move.strength
          ? `causing ${move.strength} damage!`
          : `healing ${move.recovery}.`
      });
    }
  },
  watch: {
    hps: {
      handler(newHps) {
        if (newHps.hp <= 0 && newHps.monsterHp <= 0) {
          this.result = "TIE";
        } else if (newHps.hp <= 0) {
          this.result = "DEFEAT";
        } else if (newHps.monsterHp <= 0) {
          this.result = "VICTORY";
        }
      },
      deep: true
    }
  }
};
</script>

<style>
.text-center {
  text-align: center;
}

.healthbar {
  width: 80%;
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
}

.controls,
.log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  margin: 5px;
}

.log ul .player-turn {
  color: blue;
  background-color: #e4e8ff;
}

.log ul .monster-turn {
  color: red;
  background-color: #ffc0c1;
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  margin: 10px;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal {
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
