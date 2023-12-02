<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <CalculateFees msg="Welcome to Your Vue.js App"/>
  
</template>

<script>
import CalculateFees from './components/CalculateFees.vue'

export default {
  name: 'App',
  components: {
    CalculateFees
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
  margin-top: 70px;
}
</style>
