<template>
  <div id="app">
    <header>Kozik bez pleców</header>
    <Game />
  </div>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'app',
  components: {
    Game
  }
}
</script>

<style>
* {
    box-sizing: border-box;
}

body {
    padding: 0;
    margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

header {
    background-color: rgb(19, 124, 45);
    color: #fff;
    margin: 1em 0;
    padding: 1em;
}
</style>
