<template>
  <div id="app">
    <header>OX25</header>
    <Game />
    <footer>Copyright (c) 2020 yamunaku</footer>
  </div>
</template>

<script>
import Game from "./components/Game.vue";
export default {
  name: "App",
  components: {
    Game,
  },
};
</script>

<style>
* {
  color: #c2c2c2;
  font-family: "Noto Sans JP", sans-serif;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -ms-touch-action: none;
  touch-action: none;
}

html {
  background-color: #3a3a3a;
}

header {
  width: 100%;
  padding: 20px 0;
  margin: 0 auto;
  text-align: center;
  font-family: "Quicksand", sans-serif;
  font-size: 80px;
  letter-spacing: 5px;
}

footer {
  width: 100%;
  margin: 0 auto;
  padding: 5px 0;
  background-color: #3a3a3a;
  color: #c2c2c2;
  text-align: center;
  font-family: "Quicksand", sans-serif;
  font-size: 20px;
}
</style>
