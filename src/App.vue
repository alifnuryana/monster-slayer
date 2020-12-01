<template>
  <div id="app" class="bg-indigo-500 flex flex-col justify-center items-center">
    <h1 class="mt-5 text-2xl font-semibold tracking-wider">Monster Slayer</h1>
    <!-- Healt Point -->
    <div class="max-w-lg w-full p-5 rounded bg-white mt-5 shadow-lg">
      <h1 class="border-b-2 border-solid pb-2">Healt Point</h1>
      <div class="mt-2">
        <div class="w-full bg-green-300 rounded-md overflow-hidden">
          <div
            class="max-h-15 p-1"
            v-bind:style="{ width: monster.healtPoint + '%' }"
            v-bind:class="{
              'bg-red-400': isLowMonster,
              'bg-yellow-200': isMediumMonster,
              'bg-green-400': isHighMonster,
            }"
          >
            Monster Point
          </div>
        </div>
        <div class="w-full bg-green-300 rounded-md overflow-hidden mt-2">
          <div
            class="p-1 max-h-15"
            v-bind:class="{
              'bg-red-400': isLowPlayer,
              'bg-yellow-200': isMediumPlayer,
              'bg-green-400': isHighPlayer,
            }"
            v-bind:style="{ width: player.healtPoint + '%' }"
          >
            Player Point
          </div>
        </div>
      </div>
    </div>

    <!-- Action Player -->
    <div class="max-w-lg w-full p-5 rounded bg-white mt-5 shadow-lg">
      <h1 class="border-b-2 border-solid pb-2">Action Player</h1>
      <div
        class="flex flex-col gap-y-1 md:justify-around md:flex-row md:items-center mt-2"
      >
        <button
          class="bg-indigo-500 px-4 py-2 rounded shadow-md text-white"
          v-bind:class="{ 'opacity-50': isMonsterWinner || isPlayerWinner }"
          v-on:click="basicAttack('Player')"
          v-bind:disabled="isMonsterWinner || isPlayerWinner"
        >
          Attack
        </button>
        <button
          class="bg-indigo-500 px-4 py-2 rounded shadow-md text-white"
          v-bind:class="{
            'opacity-50':
              !isSpecialAttackPlayer || isMonsterWinner || isPlayerWinner,
            'cursor-default':
              !isSpecialAttackPlayer || isMonsterWinner || isPlayerWinner,
          }"
          v-bind:disabled="
            !isSpecialAttackPlayer || isMonsterWinner || isPlayerWinner
          "
          v-on:click="specialAttack"
        >
          Special Attack
        </button>
        <button
          class="bg-indigo-500 px-4 py-2 rounded shadow-md text-white"
          v-on:click="heal"
          v-bind:class="{ 'opacity-50': isMonsterWinner || isPlayerWinner }"
          v-bind:disabled="isMonsterWinner || isPlayerWinner"
        >
          Heal
        </button>
        <button
          class="bg-indigo-500 px-4 py-2 rounded shadow-md text-white"
          v-bind:disabled="isMonsterWinner || isPlayerWinner"
          v-bind:class="{ 'opacity-50': isMonsterWinner || isPlayerWinner }"
          v-on:click="surrender"
        >
          Surrender
        </button>
      </div>
    </div>

    <!-- Log -->
    <div
      class="max-w-lg w-full p-5 rounded bg-white text-center mt-5 mb-5 shadow-lg"
    >
      <h1 class="border-b-2 mb-2 border-solid pb-2 text-left">Log</h1>
      <span class="" v-if="logs.length < 1">
        The battle hasn't started yet
      </span>
      <ul>
        <li v-for="log in logs" v-bind:key="log.id">
          <span class="mt-2" v-if="log.category === 'Winner'">
            {{ log.message }}
          </span>
          <span class="mt-2" v-else-if="log.category === 'Attack'">
            {{ log.message }}
          </span>
          <span class="mt-2" v-else-if="log.category === 'Heal'">
            {{ log.message }}
          </span>
          <span class="mt-2" v-else-if="log.category === 'Surrender'">
            {{ log.message }}
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      player: {
        healtPoint: 100,
        maxBasicAttack: 15,
        minBasicAttack: 5,
        maxSpecialAttack: 25,
        minSpecialAttack: 16,
        maxHeal: 25,
        minHeal: 15,
        totalTurn: 0,
      },
      monster: {
        healtPoint: 100,
        maxBasicAttack: 15,
        minBasicAttack: 9,
      },
      logs: [],
    };
  },
  computed: {
    isSpecialAttackPlayer() {
      return this.player.totalTurn >= 5 ? true : false;
    },
    isPlayerWinner() {
      return this.monster.healtPoint <= 0 ? true : false;
    },
    isMonsterWinner() {
      return this.player.healtPoint <= 0 ? true : false;
    },
    isLowPlayer() {
      if (this.player.healtPoint <= 15) {
        return true;
      }
      return false;
    },
    isMediumPlayer() {
      if (this.player.healtPoint > 15 && this.player.healtPoint <= 45) {
        return true;
      }
      return false;
    },
    isHighPlayer() {
      if (this.player.healtPoint > 45) {
        return true;
      }
      return false;
    },
    isLowMonster() {
      if (this.monster.healtPoint <= 15) {
        return true;
      }
      return false;
    },
    isMediumMonster() {
      if (this.monster.healtPoint > 15 && this.monster.healtPoint <= 45) {
        return true;
      }
      return false;
    },
    isHighMonster() {
      if (this.monster.healtPoint > 45) {
        return true;
      }
      return false;
    },
  },
  watch: {
    "monster.healtPoint": function (newVal, oldVal) {
      if (newVal <= 0) {
        this.monster.healtPoint = 0;
        this.logs.push({
          id: this.logs.length + 1,
          category: "Winner",
          message: "The Player Winner!",
        });
      } else if (oldVal - newVal >= 16) {
        const that = this;
        setTimeout(function () {
          that.basicAttack("Monster");
        }, 500);
      } else {
        const that = this;
        setTimeout(function () {
          that.basicAttack("Monster");
          that.player.totalTurn += 1;
        }, 500);
      }
    },
    "player.healtPoint": function (newVal, oldVal) {
      if (newVal <= 0) {
        this.player.healtPoint = 0;
        this.logs.push({
          id: this.logs.length + 1,
          category: "Winner",
          message: "The Monster Winner!",
        });
      } else if (newVal > oldVal) {
        const that = this;
        setTimeout(function () {
          that.basicAttack("Monster");
          that.player.totalTurn += 1;
        }, 500);
      }
    },
  },
  methods: {
    basicAttack(type) {
      if (type === "Player") {
        const valAttackPlayer = Math.floor(
          Math.random() *
            (this.player.maxBasicAttack - this.player.minBasicAttack) +
            this.player.minBasicAttack
        );
        this.monster.healtPoint -= valAttackPlayer;
        this.logs.push({
          id: this.logs.length + 1,
          category: "Attack",
          message: `Player hit monster ${valAttackPlayer} point.`,
        });
      } else if (type === "Monster") {
        const valAttackMonster = Math.floor(
          Math.random() *
            (this.monster.maxBasicAttack - this.monster.minBasicAttack) +
            this.monster.minBasicAttack
        );
        this.player.healtPoint -= valAttackMonster;
        this.logs.push({
          id: this.logs.length + 1,
          category: "Attack",
          message: `Monster hit player ${valAttackMonster} point.`,
        });
      }
    },
    specialAttack() {
      const valAttackPlayer = Math.floor(
        Math.random() *
          (this.player.maxSpecialAttack - this.player.minSpecialAttack) +
          this.player.minSpecialAttack
      );
      this.monster.healtPoint -= valAttackPlayer;
      this.player.totalTurn = 0;
      this.logs.push({
        id: this.logs.length + 1,
        category: "Attack",
        message: `Player hit monster with special attack, ${valAttackPlayer} point.`,
      });
    },
    heal() {
      const valHeal = Math.floor(
        Math.random() * (this.player.maxHeal - this.player.minHeal) +
          this.player.minHeal
      );
      if (this.player.healtPoint >= 70) {
        this.logs.push({
          id: this.logs.length + 1,
          category: "Heal",
          message: `You can't heal because your healt point more than ${this.player.healtPoint}.`,
        });
      } else {
        this.player.healtPoint += valHeal;
        this.logs.push({
          id: this.logs.length + 1,
          category: "Heal",
          message: `Player healing ${valHeal}.`,
        });
      }
    },
    surrender() {
      this.player.healtPoint = 0;
      this.logs.push({
        id: this.log.length + 1,
        category: "Surrender",
        message: `The player has surrender.`,
      });
    },
  },
};
</script>

<style>
</style>
