<template>
  <div>
    <button :class="classStyles" @click="emitOneMove">play one move</button>
    <button :class="classStyles" @click="emitBackMove" v-if="!winner">Back one move</button>
    <button :class="classStyles" @click="emitRestart">Restart Game</button>
    <button :class="classStyles" @click="emitPlayAlone">{{isOnePlayer?"Set Two Player":"Set One Player"}}</button>
  </div>
</template>

<script>
export default {
data: () => ({
    classStyles:"bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
}),


props:{
    isOnePlayer: Boolean,
    winner: String
},


methods: {
    emitOneMove() {
        this.$emit("handle-move");
        //console.log("Hello");
    },


    emitBackMove() {
        this.$emit("handle-back");
    },


    emitRestart() {
        this.$emit("handle-restart", {});
    },

    emitPlayAlone() {
        this.$emit("handle-play-alone");
    }
},
};
</script>
