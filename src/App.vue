<template>
  <div id="app">
    <Game />
  </div>
</template>

<script>
import Game from "./components/Game_Component.vue";

export default {
  name: "App",
  components: {
    Game
  }
};
</script>
<style>
  *{
    margin: 0;
    padding: 0;
  }
</style>