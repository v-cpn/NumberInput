<template>
  <div id="app">
    <NumberInput style="width:250px;" v-model="number" />
  </div>
</template>

<script>
import NumberInput from './NumberInput.vue'

export default {
  name: 'App',
  components: {
    NumberInput
  },
  data() {
    return {
      number: 0
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
