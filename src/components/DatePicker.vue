<template>
  <h1>Date Picker</h1>
  <div class="date-picker">
    <input
      type="text"
      v-model="formattedDate"
      @focus="toggleCalendar(true)"
      @input="updateDate"
      placeholder="__.__.____ г"
      class="date-input"
    />
    </div>
    <div v-if="isCalendarVisible" class="calendar">
      <div class="calendar-header">
        <div class="header-item">
        <button class="nav-button" @click="prevMonth">ᐸ</button>
        <span class="month" @click="toggleMonthDropdown">
          {{ months[selectedMonth] }}
          <div v-if="isMonthDropdownVisible" class="month-dropdown">
            <span v-for="(month, index) in months" :key="month" @click="selectMonth(index)">
              {{ month }}
            </span>
          </div>
        </span>
        <button class="nav-button" @click="nextMonth">ᐳ</button>
      </div>
      <div class="header-item">
        <button class="nav-button" @click="prevYear">ᐸ</button>
        <span class="year" @click="toggleYearDropdown">
          {{ selectedYear }}
          <div v-if="isYearDropdownVisible" class="year-dropdown">
            <span v-for="year in Array.from({ length: 21 }, (_, i) => selectedYear - 10 + i)" :key="year" @click="selectYear(year)">
              {{ year }}
            </span>
          </div>
        </span>
        <button class="nav-button" @click="nextYear">ᐳ</button>
      </div>
      </div>
      <div class="day-names">
        <span v-for="day in dayNames" :key="day" class="day-name">{{ day }}</span>
      </div>
      <div class="days">
        <span
          v-for="day in prevMonthDays"
          :key="day"
          :class="['day', {'weekend': isWeekendPrevMonth(day)}]"
          @click="prevMonth"
        >
        <span class="other-month">{{ day }}</span>
        </span>
        <span
          v-for="day in daysInMonth"
          :key="day"
          :class="[
            'day',
            { 'selected': day === selectedDay, 'today': isToday(day), 'weekend': isWeekendCurrentMonth(day) }
          ]"
          @click="selectDay(day)"
        >
        <span>{{ day }}</span>
        </span>
        <span
          v-for="day in nextMonthDays"
          :key="day"
          :class="['day', {'weekend': isWeekendNextMonth(day)}]"
          @click="nextMonth"
          >
          <span class="other-month">{{ day }}</span>
        </span>
      </div>
    </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const selectedYear = ref<number>(new Date().getFullYear());
const selectedMonth = ref<number>(new Date().getMonth());
const selectedDay = ref<number | null>(null);
const isCalendarVisible = ref<boolean>(false);

// NEW
const isMonthDropdownVisible = ref<boolean>(false);
const isYearDropdownVisible = ref<boolean>(false);
// END NEW

const months = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"];
const dayNames = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"];

const daysInMonth = computed(() => {
  const days = new Date(selectedYear.value, selectedMonth.value + 1, 0).getDate();
  return Array.from({ length: days }, (_, i) => i + 1);
});

// NEW
const prevMonthDays = computed(() => {
  const firstDayOfMonth = new Date(selectedYear.value, selectedMonth.value, 1).getDay();
  const prevMonthDate = new Date(selectedYear.value, selectedMonth.value, 0).getDate();
  return Array.from({ length: (firstDayOfMonth + 6) % 7 }, (_, i) => prevMonthDate - i).reverse();
});

const nextMonthDays = computed(() => {
  const daysInCurrentMonth = daysInMonth.value.length;
  const remainingDays = 42 - daysInCurrentMonth - prevMonthDays.value.length;
  return Array.from({ length: remainingDays }, (_, i) => i + 1);
});
// END NEW

const formattedDate = computed({
  get: () => {  
    if (selectedDay.value === null) return '';
    return `${String(selectedDay.value).padStart(2, '0')}.${String(selectedMonth.value + 1).padStart(2, '0')}.${selectedYear.value}`;
  },
  set: (value: string) => {
    const [day, month, year] = value.split('.').map(Number);
    if (day && month && year) {
      selectedDay.value = day;
      selectedMonth.value = month - 1;
      selectedYear.value = year;
    }
  }
});

const toggleCalendar = (visible: boolean) => {
  isCalendarVisible.value = visible;
};

// NEW
const toggleMonthDropdown = () => {
  isMonthDropdownVisible.value = !isMonthDropdownVisible.value;
  isYearDropdownVisible.value = false;
};

const toggleYearDropdown = () => {
  isYearDropdownVisible.value = !isYearDropdownVisible.value;
  isMonthDropdownVisible.value = false;
};

const selectMonth = (month: number) => {
  selectedMonth.value = month;
  setTimeout(() => {
    isMonthDropdownVisible.value = false;
  });
};

const selectYear = (year: number) => {
  selectedYear.value = year;
  setTimeout(() => {
    isYearDropdownVisible.value = false;
  })
};
// END NEW

const selectDay = (day: number) => {
  selectedDay.value = day;
  toggleCalendar(false);
};

const prevMonth = () => {
  if (selectedMonth.value === 0) {
    selectedMonth.value = 11;
    selectedYear.value -= 1;
  } else {
    selectedMonth.value -= 1;
  }
};

const nextMonth = () => {
  if (selectedMonth.value === 11) {
    selectedMonth.value = 0;
    selectedYear.value += 1;
  } else {
    selectedMonth.value += 1;
  }
};

const prevYear = () => {
  selectedYear.value -= 1;
};

const nextYear = () => {
  selectedYear.value += 1;
};

const updateDate = (event: Event) => {
  const input = event.target as HTMLInputElement;
  formattedDate.value = input.value;
};

const isToday = (day: number) => {
  const today = new Date();
  return (
    today.getFullYear() === selectedYear.value && 
    today.getMonth() === selectedMonth.value &&
    today.getDate() === day
  );
};

const isWeekendCurrentMonth = (day: number) => {
  const date = new Date(selectedYear.value, selectedMonth.value, day);
  return date.getDay() === 6 || date.getDay() === 0;
};

const isWeekendPrevMonth = (day: number) => {
  const date = new Date(selectedYear.value, selectedMonth.value - 1, day);
  return date.getDay() === 6 || date.getDay() === 0;
};

const isWeekendNextMonth = (day: number) => {
  const date = new Date(selectedYear.value, selectedMonth.value + 1, day);
  return date.getDay() === 6 || date.getDay() === 0;
};
</script>

<style lang="scss" scoped>
$date-picker-border: #dcdcdcb0;
$selected-day-bg: #5a64f0;
$today-bg: #a2c8ff;
$weekend-color: #e60000;
$font-color: #333;

.date-picker {
  position: relative;
  width: 230px;
  height: 30px;
  font-family: sans-serif;
  margin: 17px;
  
  .date-input {
    width: 100%;
    height: 100%;
    padding: 6px;
    border: 1px solid $date-picker-border;
    border-radius: 7px;
    font-size: 25px;
    outline: none;
    background-image: url('./img/calendar.png');
    background-size: 20px;
    background-position: left 17px center;
    background-repeat: no-repeat;
    padding-right: 30px;
    text-indent: 40px;
  }
}

  .calendar {
    display: flex;
    flex-direction: column;
    align-items: center;
    position: sticky;
    top: 131px;
    width: 500px;
    height: 500px;
    background-color: white;
    border: 1px solid $date-picker-border;
    border-radius: 7px;
    padding: 10px;
    font-family: sans-serif;
    margin: 17px;

    .calendar-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
      padding-left: 7px;
      padding-right: 7px;
      width: 445px;
      height: 70px;

    .header-item {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100%;
    }

      .month, .year {
        font-size: 27px;
        font-weight: bold;
        color: $font-color;
        position: relative;
        cursor: pointer;
        padding: 0 12px;
      }

      .nav-button {
        background: none;
        border: none;
        font-size: 23px;
        cursor: pointer;
        color: $font-color;
      }

      .month-dropdown, .year-dropdown {
        position: absolute;
        background: white;
        border: none;
        padding: 2px;
        z-index: 2;
        width: auto;
        max-height: 240px;
        overflow-y: auto;
        opacity: 0.8;

        span {
          display: block;
          padding: 5px;
          cursor: pointer;
          &:hover {
            background: #e6e6e6;
          }
        }
      }
 
    }

    .day-names {
      width: 90%;
      font-size: 20px;
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      text-align: center;
      margin-bottom: 10px;
      color: $font-color;
    }

    .days {
      border: 0.5px solid $date-picker-border;
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      width: 90%;
      height: 70%;

      .day {
        border: 0.5px solid $date-picker-border;
        padding: 8px;
        cursor: pointer;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 20px;

        &.selected {
          background-color: $selected-day-bg;
          color: white;
        }

        &.today {
          text-decoration: underline;
        }

        &.weekend {
          color: $weekend-color;
        }

        .other-month {
          opacity: 0.25;
        }

        &:hover {
          background-color: #a89aeb60;
        }
      }
    }
  }

</style>
