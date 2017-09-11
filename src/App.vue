<template>
  <div id="app">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">YOU</h1>
            <div class="healthbar">
                <div class="healthbar text-center" 
                  style="background-color: green; margin: 0; color: white;"
                  :style="{ width: playerHealth + '%' }" >
                  {{ playerHealth }}
                </div>
            </div>
        </div>


        <div class="small-6 columns">
            <h1 class="text-center">MONSTER</h1>
            <div class="healthbar">
                <div class="healthbar text-center" 
                  style="background-color: green; margin: 0; color: white;"
                  :style="{ width: monsterHealth + '%' }" >
                  {{ monsterHealth }}
                </div>
            </div>
        </div>
    </section>


    <section class="row controls" v-if="! gameStarted">
        <div class="small-12 columns">
            <button id="start-game"
              @click="startGame()" >
              START NEW GAME
            </button>
        </div>
    </section>


    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button id="attack"
              @click="playerAttack()" >
              ATTACK
            </button>

            <button id="special-attack"
              @click="playerSpecial()" >
              SPECIAL ATTACK
            </button>

            <button id="heal"
              @click="heal()" >
              HEAL
            </button>

            <button id="give-up"
              @click="giveUp()" >
              GIVE UP
            </button>
        </div>
    </section>


    <section class="row log" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="(turn, id) in turns" 
                  :key="id" 
                  :class="{'player-turn': turn.isPlayer, 'monster-turn': ! turn.isPlayer }" >
                  {{ turn.text }}
                </li>
            </ul>
        </div>
    </section>
  </div>
</template>

<script>
let maxHealth = 100;

export default {
  name: 'app',

  data: () => ({
    playerHealth: maxHealth,
    monsterHealth: maxHealth,
    gameStarted: false,
    turns: []
  }),

  methods: {
    startGame() {
      this.resetHealth();
      this.gameStarted = true;
      this.resetTurns();
    },

    
    resetTurns() {
      this.turns = [];
    },


    resetHealth() {
      this.playerHealth = maxHealth;
      this.monsterHealth = maxHealth;
    },

    
    randomDamage(min, max) {
      return Math.max(
        Math.floor((Math.random() * max) + 1), 
        min);
    },


    playerAttack() {
      let damage = this.randomDamage(3, 10);
      this.monsterHealth -= damage;
      this.addTurn(true, 'Player attacks Monster for ', damage);

      if(this.playerWins()){
        return;
      }

      this.monsterAttack();
    },


    playerSpecial() {
      let damage = this.randomDamage(10, 20);
      this.monsterHealth -= damage;
      this.addTurn(true, 'Player special attacks Monster for ', damage);

      if(this.playerWins()){
        return;
      }

      this.monsterAttack();
    },


    monsterAttack() {
      let damage = this.randomDamage(5, 12);
      this.playerHealth -= damage;
      this.addTurn(false, 'Monster attacks Player for ', damage);

      this.monsterWins();
    },


    giveUp() {
      this.gameStarted = false;
    },

    
    heal() {
      let health = 10;

      if (this.playerHealth <= 90) {		
        this.playerHealth += health;		
      } else {		
        this.playerHealth = maxHealth;		
      }

      this.addTurn(true, 'Player heals for ', health);

      this.monsterAttack();
    },


    playerWins() {
      if(this.monsterHealth <= 0 && this.playerHealth > 0) {
        this.startAgain('Player Wins, Start Again?');
        return true;
      }

      return false;
    },


    monsterWins() {
      if(this.playerHealth <= 0 && this.monsterHealth > 0) {
        this.startAgain('Monster Wins, Start again?');
      }
    },


    startAgain(message) {
      if(confirm(message)) {
        this.startGame();
      } else {
        this.gameStarted = false;
      }
    },


    addTurn(isPlayer, text, value) {
      this.turns.unshift({		
        isPlayer: isPlayer,		
        text: text + value		
      });
    }


  },
}
</script>
