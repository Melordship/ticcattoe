<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <TicTacToeGame/>
</template>

<script>
import TicTacToeGame from './components/TicTacToeGame.vue'

export default {
  name: 'App',
  components: {
    TicTacToeGame
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
