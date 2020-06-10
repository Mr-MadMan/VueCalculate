<template>
  <div id="app">
    <calculator />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

export default {
  name: "App",
  components: {
    Calculator
  }
};
</script>

<style>
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #eee;
}
</style>
