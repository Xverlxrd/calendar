<template>
  <div class="wrapper">
    <Calendar v-model:selectedDate="date" />
  </div>
</template>

<script setup lang="ts">
import Calendar from "./components/Calendar.vue";
import {ref} from "vue";

const date = ref(new Date('2025-09-01'));

</script>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>