<template>
  <div id="app">
    <h1>Vue Snake</h1>
    <SnakeGame />
  </div>
</template>

<script setup>
import SnakeGame from './components/SnakeGame.vue';
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>