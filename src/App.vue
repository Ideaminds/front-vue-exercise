<template>
  <div
    id="app-main"
    class="app px-4 pt-4 text-indigo-50 min-h-screen bg-slate-900 grid gap-4 items-center mx-auto sm:max-w-xl lg:max-w-5xl"
  >
    <h1
      class="text-center text-2xl border-2 border-indigo-300 rounded mb-4 text-indigo-200"
    >
      TIC TAC TOE
    </h1>
    <p
      class="info text-slate-600 bg-slate-800 mb-4 px-4 py-1 rounded text-center"
    >
      {{ isPlayAlone ? "play alone" : "two players" }}
      <i class="fas fa-user"></i>
      <i class="fas fa-user" v-if="!isPlayAlone"></i>
    </p>
    <div
      class="board grid grid-rows-3 grid-cols-3 gap-2 mb-4 lg:w-3/4"
      style="aspect-ratio: 1 / 1"
    >
      <div
        v-for="(value, key) in values"
        :key="key"
        @click="!isPlayAlone ? twoPlayers(key): playAlone(key)"
        class="bg-pink-800 hover:bg-pink-700 rounded flex justify-center items-center"
      >
        <span v-if="!value">{{ value }}</span>
        <i v-else :class="`${turn[value]} text-4xl`"></i>
      </div>
    <pre>{{`${previous}, ${current}`}}</pre>
    </div>
    <menu-board
      v-on:handle-move="makeOneMove"
      v-on:handle-back="backOneMove"
      v-on:handle-restart="restartValues"
      v-on:handle-play-alone="setOnePlayer"
      :isPlayAlone="isPlayAlone"
      :winner="turn[winner]"
      class="menu"
    />
    <!-- <modal-winner
      :winner="turn[winner]"
      v-if="!!winner"
      v-on:handle-restart="setValues"
    /> -->
    <footer class="text-xs text-slate-700 text-center flex flex-col">
      <span
        >developed by
        <a
          href="https://github.com/AlbertDeHoz/front-vue-exercise"
          class="text-slate-600"
          >AlbertDeHoz{<i class="fab fa-github"></i>}</a
        >
        with <i class="fas fa-heart"></i
      ></span>
    </footer>
  </div>
</template>

<script>
// import ModalWinner from "./components/ModalWinner";
import MenuBoard from "./components/MenuBoard";

export default {
  name: "App",

  components: {
    MenuBoard,
    // ModalWinner
  },

  data: () => ({
    isPlayAlone: false,
    turn: { X: "fa fa-times", O: "fa fa-circle" },
    history: [],
    values: {
      1: "",
      2: "",
      3: "",
      4: "",
      5: "",
      6: "",
      7: "",
      8: "",
      9: "",
    },
    slotStyles: {
      1: "border-b-4 border-r-4",
      2: "border-r-4 border-l-4 border-b-4",
      3: "border-b-4 border-l-4",
      4: "border-r-4 border-t-4 border-b-4",
      5: "border-4",
      6: "border-l-4 border-t-4 border-b-4",
      7: "border-t-4 border-r-4",
      8: "border-l-4 border-t-4 border-r-4",
      9: "border-t-4 border-l-4",
    },
    current: "X",
    previous: "O",
    winner: null,
  }),

  methods: {
    restartValues(valuesDefault){
      this.setValues(valuesDefault);
      this.current="X";
      this.previous = "O";
    },
    
    setOnePlayer() {
      this.isPlayAlone = !this.isPlayAlone;
    },

    toggleCurrent() {
      this.current = this.current === "X" ? "O" : "X";
    },

    togglePrevious() {
      this.previous = this.previous !== "O" ? "O" : "X";
      console.log('once')
    },

    makeOneMove(previousMove) {
      const emptySlots = this.getEmptySlots();
      const randomNumber = Math.floor(Math.random() * emptySlots.length);
      const key = emptySlots[randomNumber];
      return new Promise((resolve) => {
        if (previousMove) {
          setTimeout(()=>{
            // this.play(key)
            resolve(this.play(key))
          },1000);
        }
      });
    },

    backOneMove() {
      this.history.pop();
      const lastIndex = this.history.length - 1;
      this.setValues({ ...this.history[lastIndex] });
    },

    getEmptySlots() {
      const emptySlots = [];
      for (const [key, value] of Object.entries(this.values)) {
        if (!value) {
          emptySlots.push(key);
        }
      }
      return emptySlots;
    },

    setValues({ ...values }) {
      // this.history.length == 0 means no movements yet
      // '!values' means reset values due to 'handle-restart' sets the 'values' params as null
      // 'this.winner' means a player has won
      const isValuesEmpty =
        Object.keys(values).length === 0 && values.constructor === Object;

      if (this.history.length == 0 || (isValuesEmpty && this.winner)) {
        for (let key = 1; key <= 9; key++) {
          this.values[key] = "";
          this.history = [];
          this.current = "X";
          this.winner = this.calculateWinner(this.values);
        }
        return;
      }
      this.values = values;
      this.winner = this.calculateWinner(this.values);
    },

    async playAlone(key) {
      if (this.current === this.previous) {
        return
      }
      const playerMove = this.play(key);
      await this.makeOneMove(playerMove)
    },

    twoPlayers(key) {
      this.play(key);
      this.togglePrevious();
    },

    sleep(time) {
      return new Promise((resolve) => setTimeout(resolve, time));
    },

    play(key) {
      if (this.values[key] || !!this.winner) {
        return;
      }
      this.values[key] = this.current;
      this.winner = this.calculateWinner(this.values);
      this.history.push({ ...this.values });
      this.toggleCurrent();
      return true;
    },

    /*
     * calculateWinner  receives the status of the current value
     * and returns a winner if it exists
     */
    calculateWinner(values) {
      const lines = [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9],
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9],
        [1, 5, 9],
        [3, 5, 7],
      ];
      for (let i = 0; i < lines.length; i++) {
        const [a, b, c] = lines[i];
        if (values[a] && values[a] === values[b] && values[a] === values[c]) {
          return values[a];
        }
      }
      return null;
    },
  },
};
</script>

<style>
#app-main {
  font-family: "Roboto Mono", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app h1 {
  grid-area: title;
}

.app .info {
  grid-area: info;
  align-self: end;
}

.app .board {
  grid-area: board;
}

.app .menu {
  grid-area: menu;
  align-self: start;
}
.app footer {
  grid-area: footer;
}

.app {
  grid-template-areas:
    "title"
    "info"
    "board"
    "menu"
    "footer";
}

@media (min-width: 64rem) {
  .app {
    grid-template-columns: repeat(3, 1fr);
    grid-template-areas:
      "title title title"
      "board board info"
      "board board menu"
      "footer footer footer";
  }
}
</style>
