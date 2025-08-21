<template>
  <div class="wrapper">
    <Calendar/>
  </div>
</template>

<script setup lang="ts">

import Calendar from "./components/Calendar.vue";
</script>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>