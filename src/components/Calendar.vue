<template>
  <div class="calendar">
    <div class="calendar_header">
      <span @click="prevMonth">-</span>
      <span>{{currentMonthName}} {{ currentYear }}</span>
      <span @click="nextMonth">+</span>
    </div>

    <div class="calendar_content">
      <div class="day_of_week" v-for="day in daysOfWeek" :key="day">{{day}}</div>

      <div
          v-for="notDay in firstDayOffset"
          :key="notDay"
          class="empty_day"
      ></div>

      <span
        v-for="day in calendarDays"
        :key="day.date.toString()"
      >
        {{day.date.getDate()}}
      </span>
    </div>

    <h4>Selected date: {{}}</h4>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

const daysOfWeek = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
const currentDate = ref(new Date());

const currentYear = computed(() => currentDate.value.getFullYear());
const currentMonth = computed(() => currentDate.value.getMonth());
const currentMonthName = computed(() =>
    currentDate.value.toLocaleString('en', { month: 'short' })
);
// const selectedDate = ref(null);
// const selectDay = (day: number) => {
// }

const firstDayOffset = computed(() =>
    new Date(currentYear.value, currentMonth.value, 1).getDay()
);

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear();
  const month = currentDate.value.getMonth();
  const lastDay = new Date(year, month + 1, 0);
  const today = new Date();

  const days = [];
  for (let i = 1; i <= lastDay.getDate(); i++) {
    const date = new Date(year, month, i);
    days.push({
      date,
      isToday: date.toDateString() === today.toDateString()
    });
  }

  return days;
});

const prevMonth = () => {
  currentDate.value = new Date(
      currentDate.value.getFullYear(),
      currentDate.value.getMonth() - 1,
      1
  );
};

const nextMonth = () => {
  currentDate.value = new Date(
      currentDate.value.getFullYear(),
      currentDate.value.getMonth() + 1,
      1
  );
};

onMounted(() => {
  currentDate.value = new Date();
});
</script>

<style scoped>
.calendar {
  width: 300px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  border: 1px solid #CDCDCD;
  padding: 10px;
}
.calendar_header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.calendar_content {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
  row-gap: 10px;
}
.day_of_week {
}
</style>