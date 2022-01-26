<template>
  <div class="w-full h-screen flex flex-col items-center justify-center">
    <div class="grid gap-4 grid-cols-3">
      <div
        v-for="(value, key) in values"
        :key="key"
        @click="isOnePlayer?playAlone(key):play(key)"
        :class="slotStyles[key] + ' w-48 h-48 flex items-center justify-center text-4xl hover:bg-gray-100 cursor-pointer'"
      >
        <span v-if="!value">{{ value }}</span>
        <i v-else :class="turn[value]"></i>
      </div>
    </div>
	<menu-board
	v-on:handle-move="makeOneMove"
	v-on:handle-back="backOneMove"
	v-on:handle-restart="setValues"
	v-on:handle-play-alone="setOnePlayer"
	:isOnePlayer="isOnePlayer"
	:winner="winner"
	/>
	<modal-winner
	:winner="winner"
	v-on:handle-restart="setValues"
	v-if="!!winner"
	/>
  </div>
</template>

<script>
import ModalWinner from './components/ModalWinner'
import MenuBoard from './components/MenuBoard'

export default {
	name: "App",

	components: { ModalWinner,MenuBoard },

	data: () => ({
		isOnePlayer: false,
		turn: { X: "fas fa-times", O: "fas fa-circle" },
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
		winner: null
	}),

	methods: {
		setOnePlayer() {
			this.isOnePlayer = !this.isOnePlayer
			this.$swal('Heading', 'this is a Heading', 'OK')
		},


		makeOneMove() {
			const emptySlots = this.getEmptySlots();
			const randomNumber = Math.floor(Math.random() * emptySlots.length);
			const key = emptySlots[randomNumber];
			this.play(key);
		},


		backOneMove() {
			this.current = this.current === "X" ? "O" : "X";
			this.history.pop();
			const lastIndex = this.history.length - 1;
			this.setValues( {...this.history[lastIndex]} );
			console.log(this.history);

		},


		getEmptySlots () {
			const emptySlots = [];
			for (const [key, value] of Object.entries(this.values)) {
				if (!value){ emptySlots.push(key)}
			}
			return emptySlots;
		},


		setValues({...values}) {

			// this.history.length == 0 means no movements yet
			// '!values' means reset values due to 'handle-restart' sets the 'values' params as null
			// 'this.winner' means a player has won
			console.log(values);
			const isValuesEmpty = Object.keys(values).length === 0 && values.constructor === Object;

			if (this.history.length == 0 || isValuesEmpty || this.winner) {
				for (let key=1; key<=9; key++){
					this.values[key] = "";
					this.history = [];
					this.current = "X";
					this.winner = this.calculateWinner(this.values);
				}
				return 0;
			}
			this.values = values;
			this.winner = this.calculateWinner(this.values);
		},


		playAlone(key) {
			this.play(key);
			console.log(this.getEmptySlots().length)
			if( this.getEmptySlots().length !== 0) {
				this.makeOneMove();
			}
		},


		play(key) {
			if (!this.values[key] && !this.winner) {
				this.values[key] = this.current;
				this.current = this.current === "X" ? "O" : "X";
				this.winner = this.calculateWinner(this.values);
				this.history.push({...this.values});
				console.log(this.history)
			}
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
				if (values[a] && values[a] === values[b] && values[a] === values[c] ) {
					return values[a];
				}
			}
			return null;
		},
	},
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.fas {
  font-size: 5rem;
}
</style>
