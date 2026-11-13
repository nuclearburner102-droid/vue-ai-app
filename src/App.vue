<template>
  <div class="app">
    <RockPaperScissors />
  </div>
</template>

<script setup>
import RockPaperScissors from './RockPaperScissors.vue'
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(160deg, #0f172a 0%, #1e1b4b 100%);
  font-family: system-ui, -apple-system, sans-serif;
}

.app {
  width: 100%;
  padding: 1.5rem;
}
</style>