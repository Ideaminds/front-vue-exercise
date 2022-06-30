<template>
  <div class="grid grid-cols-3 gap-2 mb-4">
    <button :class="classStyles" @click="emitBackMove" :disabled="!!winner"><i class="fas fa-step-backward"></i></button>
    <button :class="classStyles" @click="emitOneMove" title="play one move" :disabled="!!winner"><i class="fas fa-play"></i></button>
    <button :class="classStyles" @click="emitRestart"><i class="fas fa-undo"></i></button>
    <button :class="classStyles" @click="emitPlayAlone" style="grid-column: 1 / span 3">{{isPlayAlone?"Set Two Player":"Set Play Alone"}}</button>
  </div>
</template>

<script>
export default {
data: () => ({
    classStyles:"hover:bg-indigo-500 text-indigo-400 bg-indigo-900 hover:text-indigo-50 py-2 px-4 rounded disabled:bg-slate-800 disabled:text-slate-600 sm:text-xl"
}),


props:{
    isPlayAlone: Boolean,
    winner: String
},


methods: {
    emitOneMove() {
        this.$emit("handle-move");
    },


    emitBackMove() {
        this.$emit("handle-back");
    },


    emitRestart() {
        this.$emit("handle-restart");
    },

    emitPlayAlone() {
        this.$emit("handle-play-alone");
    }
},
};
</script>
