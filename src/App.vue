<template>
  <div id="app">
    <zuopaiVue />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import zuopaiVue from "./views/zuopai.vue";

export default Vue.extend({
  components: {
    zuopaiVue
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
