<template>
  <div class="calendar">
    <div class="language_selector">
      <select v-model="selectedLanguage">
        <option v-for="lang in availableLanguages" :key="lang.code" :value="lang.code">
          {{ lang.name }}
        </option>
      </select>
    </div>

    <div class="calendar_header">
      <span @click="prevMonth">◀️</span>
      <span>{{ currentMonthName }} {{ currentYear }}</span>
      <span @click="nextMonth">▶️</span>
    </div>

    <div class="calendar_content">
      <div class="day_of_week" v-for="day in daysOfWeek" :key="day">{{ day }}</div>

      <div
          v-for="notDay in firstDayOffset"
          :key="'empty-' + notDay"
          class="empty_day"
      ></div>

      <span
          v-for="day in calendarDays"
          :key="day.date.toString()"
          class="calendar_day"
          :class="{ 'today': day.isToday, 'selected': isSelected(day.date) }"
          @click="selectDate(day.date)"
      >
        {{ day.date.getDate() }}
      </span>
    </div>

    <h4>{{ formattedSelectedDate }}</h4>
  </div>
</template>

<script setup lang="ts">
import {ref, computed, onMounted, watch} from 'vue';

interface Props {
  selectedDate?: Date | null;
}
interface Language {
  code: string;
  name: string;
}
interface CalendarDay {
  date: Date;
  isToday: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  selectedDate: null
});

const emit = defineEmits<{
  'update:selectedDate': [date: Date | null]
}>();

const availableLanguages: Language[] = [
  {code: 'en', name: 'English'},
  {code: 'ru', name: 'Русский'},
  {code: 'de', name: 'Deutsch'},
  {code: 'fr', name: 'Français'},
  {code: 'es', name: 'Español'},
];


const selectedLanguage = ref<string>('en');
const currentDate = ref<Date>(new Date());
const internalSelectedDate = ref<Date | null>(null);


const currentYear = computed(():number => currentDate.value.getFullYear());
const currentMonth = computed(():number => currentDate.value.getMonth());
const currentMonthName = computed(():string => monthNames.value[currentMonth.value]);
const daysOfWeek = computed(():string[] => shortDayNames.value);


const monthNames = computed(():string[] => {
  const format = new Intl.DateTimeFormat(selectedLanguage.value, {month: 'long'});
  return Array.from({length: 12}, (_, i) =>
      format.format(new Date(2023, i, 1))
  );
});
const shortDayNames = computed(():string[] => {
  const format = new Intl.DateTimeFormat(selectedLanguage.value, {weekday: 'short'});
  return Array.from({length: 7}, (_, i) =>
      format.format(new Date(2023, 0, i + 2))
  );
});
const firstDayOffset = computed(():number =>
    new Date(currentYear.value, currentMonth.value, 0).getDay()
);
const calendarDays = computed(():CalendarDay[] => {
  const lastDay:Date = new Date(currentYear.value, currentMonth.value + 1, 0);
  const today:Date = new Date();

  return Array.from({length: lastDay.getDate()}, (_, i:number):CalendarDay => {
    const date:Date = new Date(currentYear.value, currentMonth.value, i + 1);
    return {
      date,
      isToday: date.toDateString() === today.toDateString()
    };
  });
});


const formattedSelectedDate = computed(():string => {
  if (!internalSelectedDate.value) return 'None';

  return new Intl.DateTimeFormat(selectedLanguage.value, {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(internalSelectedDate.value);
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
const selectDate = (date: Date) => {
  internalSelectedDate.value = date;
  emit('update:selectedDate', date);
};
const isSelected = (date: Date) => {
  return internalSelectedDate.value &&
      date.toDateString() === internalSelectedDate.value.toDateString();
};


watch(() => props.selectedDate, (newDate:Date | null) => {
  if (newDate) {
    internalSelectedDate.value = new Date(newDate);
    currentDate.value = new Date(newDate.getFullYear(), newDate.getMonth(), 1);
  } else {
    internalSelectedDate.value = null;
    currentDate.value = new Date();
  }
}, {immediate: true});
onMounted(() => {
  if (!props.selectedDate) {
    internalSelectedDate.value = new Date();
    emit('update:selectedDate', internalSelectedDate.value);
  }
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

.language_selector {
  display: flex;
  align-items: center;
  gap: 10px;
}

.language_selector select {
  padding: 5px;
  border: 1px solid #CDCDCD;
  border-radius: 4px;
}

.calendar_header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-weight: bold;
}

.calendar_header span {
  cursor: pointer;
  padding: 5px;
  user-select: none;
}

.calendar_header span:hover {
  background-color: #f0f0f0;
  border-radius: 4px;
}

.calendar_content {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
  row-gap: 10px;
}

.day_of_week {
  font-weight: bold;
  color: #666;
  padding: 5px 0;
}

.empty_day {
  padding: 8px 0;
}

.calendar_day {
  padding: 8px 0;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.calendar_day:hover {
  background-color: #e3f2fd;
}

.today {
  background-color: #2196f3;
  color: white;
  font-weight: bold;
}

.today:hover {
  background-color: #1976d2;
}

.selected {
  background-color: #4caf50;
  color: white;
  font-weight: bold;
}

.selected:hover {
  background-color: #388e3c;
}
</style>