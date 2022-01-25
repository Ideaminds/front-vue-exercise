<template>
    <div class="w-full h-screen flex items-center justify-center">
        <div class="grid gap-4 grid-cols-3">
            <div v-for="(value,key) in values" :key="key"
                 @click="play(key)"
                 class="border w-48 h-48 flex items-center justify-center text-4xl hover:bg-gray-100 cursor-pointer">
                {{ value }}
            </div>
        </div>
    </div>
    <!-- <pre>{{values}}</pre> -->
</template>

<script>

export default {
    name: "App",
    data: () => ({
            values: {
                1: '',
                2: '',
                3: '',
                4: '',
                5: '',
                6: '',
                7: '',
                8: '',
                9: '',
            },
            current: 'X'
        }),
        methods:{
            play(key){
                if(! this.values[key]) {
                    this.values[key] = this.current
                    this.current  = (this.current === 'X' ? 'O' : 'X')
                }
            }
        }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
