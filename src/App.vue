<script setup lang="ts">
import { RouterView } from 'vue-router'
import TodoNav from './components/TodoNav.vue'
</script>

<template>
  <TodoNav />
  <RouterView />
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  max-width: 1100px;
  margin: 0 auto;
}
</style>
