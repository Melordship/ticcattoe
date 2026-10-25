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
* { box-sizing: border-box; }
html { background: #f3f1e8; }
body { margin: 0; min-width: 320px; }
button, input { font: inherit; }
#app {
  min-height: 100vh;
  font-family: 'Trebuchet MS', 'Segoe UI', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #173c38;
  background:
    linear-gradient(rgba(23, 60, 56, 0.025) 1px, transparent 1px),
    linear-gradient(90deg, rgba(23, 60, 56, 0.025) 1px, transparent 1px),
    #f3f1e8;
  background-size: 32px 32px;
}
</style>
