<template>
  <div class="absolute bottom-0 left-0 bg-gray-900 w-full h-full opacity-50 flex">
    <div class="bg-white w-6/12  mx-auto my-auto flex flex-col opacity-100 py-5">
      <div class="mb-2 mx-auto">The player {{winner}} has won</div>
      <button :class="buttonStyle" @click="emitRestart" >Restart</button>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        winner: String,
    },


    data: () => ({
        buttonStyle:"bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded w-4/12 mx-auto",
    }),

    methods: {
        emitRestart() {
            this.$emit("handle-restart", {});
        },
    }
};
</script>
