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
                  :style="{width: monsterHealth + '%'}" >
                    {{ monsterHealth }}
                </div>
            </div>
        </div>
    </section>


    <section class="row controls" v-if="! gameIsRunning">
        <div class="small-12 columns">
            <button 
              id="start-game" 
              @click="startGame" >
              START NEW GAME
            </button>
        </div>
    </section>


    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button 
              id="attack"
              @click="attack" >ATTACK</button>

            <button 
              id="special-attack"
              @click="specialAttack" >SPECIAL ATTACK</button>

            <button 
              id="heal"
              @click="heal" >HEAL</button>

            <button 
              id="give-up" 
              @click="giveUp" >GIVE UP</button>
        </div>
    </section>


    <section v-if="turns.length > 0" class="row log">
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
export default {
  name: 'app',

  data: () => ({
    playerHealth: 100,
    monsterHealth: 100,
    gameIsRunning: false,
    turns: []
  }),

  methods: {
    startGame() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.turns = [];
    },

    calculateDamage(min, max) {
      return Math.max(
        Math.floor((Math.random() * max) + 1), 
        min);
    },

    
    attack() {
      let damage = this.calculateDamage(3, 10);
      this.monsterHealth -= damage;

      this.turns.unshift({
        isPlayer: true,
        text: 'Player hits Monster for ' + damage
      });

      if(this.checkWin()) {
        return;
      }

      this.monsterAttacks();
    },


    checkWin() {
      if(this.monsterHealth <= 0) {
        if(confirm('you won')) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        } 

        return true;
      } else if(this.playerHealth <= 0) {
        if(confirm('you lose')) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        } 

        return true;
      }

      return false;
    },


    specialAttack() {
      let damage = this.calculateDamage(10, 20);
      this.monsterHealth -= damage;

      this.turns.unshift({
        isPlayer: true,
        text: 'Player hits Monster hard for ' + damage
      });

      if(this.checkWin()) {
        return;
      }

      this.monsterAttacks();
    },


    monsterAttacks() {
      let damage = this.calculateDamage(5, 12);
      this.playerHealth -= damage;

      this.checkWin();

      this.turns.unshift({
        isPlayer: false,
        text: 'Monster hits Player for ' + damage
      });
    },


    heal() {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10;
      } else {
        this.playerHealth = 100;
      }

      this.turns.unshift({
        isPlayer: true,
        text: 'Player heals for 10'
      });
      
      this.monsterAttacks();
    },


    giveUp() {
      this.gameIsRunning = false;
      this.turns = [];
    }

  }
}
</script>
