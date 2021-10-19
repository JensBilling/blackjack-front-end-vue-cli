<template>
  <div id="app">
    <img alt="Blackjack logo" src="./assets/logo.png">
    <GameField></GameField>
  </div>
</template>

<script>
import GameField from "./components/GameField";

export default {
  name: 'App',
  components: {
    GameField
  }
}
</script>

<style>
body{
  background: rgb(34 177 76);
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgb(255 201 14);
  background: rgb(34 177 76);
  margin-top: 10px;

}
</style>
