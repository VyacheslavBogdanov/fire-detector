<template>
  <h1>Dropdown с поиском</h1>
  <div class="search-container">
    <input
      required 
      type="text"
      v-model="searchQuery"
      @blur="handleBlur"
      :class="{ 'has-dropdown': searchQuery && filteredList.length }"
    />
    <label class="placeholder">Комментарий</label>
    <div :class="{ 'iconPositionTop': searchQuery && filteredList.length, 'iconPositionDown': !searchQuery || !filteredList.length }">⌃</div>
    <div v-if="searchQuery && filteredList.length" class="dropdown">
      <ul class="dropdown-list">
        <li v-for="(item, index) in filteredList" :key="index" @mousedown.prevent="selectItem(item)">
          <span v-html="highlightMatch(item)"></span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">

import { ref, watch } from 'vue';

const searchQuery = ref<string>('');
const filteredList = ref<string[]>([]);

const items = ref<string[]>([
  "Смирнов А.В.", "Иванов Д.С.", "Кузнецов Е.М.", "Попов Н.А.", "Лебедев И.П.", "Козлов В.Н.",
  "Новиков А.Д.", "Морозов С.А.", "Петров Е.В.", "Васильев А.С.", "Соколов В.И.", "Михайлов О.В.",
  "Фёдоров Д.Л.", "Орлов И.К.", "Волков А.А.", "Андреев П.С.", "Никитин О.В.", "Захаров А.И.", "Куликов Д.П.",
  "Александров С.В.", "Дмитриев В.Н.", "Ковалёв Е.М.", "Ситников Л.П.", "Григорьев В.Д.", "Гордеев А.С.", "Антонов И.Н.",
  "Ефимов В.П.", "Тимофеев Д.В.", "Филиппов Е.С.", "Макаров О.А.", "Сидоров В.Д.", "Чернов И.П.", "Савельев Н.В.", "Павлов А.С.",
  "Богданов С.К.", "Мартынов Е.В.", "Воробьёв А.М.", "Антипов Д.А.", "Тарасов В.О.", "Беляев Л.В.", "Комаров И.С.", "Мельников Е.К.",
  "Шевченко С.В.", "Емельянов О.П.", "Князев В.А.", "Белов Е.И.", "Щербаков С.Д.", "Назаров Д.В.", "Кочетов О.С.", "Афанасьев Н.А."
]);

function debounce<T extends (...args: any[]) => void>(func: T, wait: number): (...args: Parameters<T>) => void {
  let timeout: ReturnType<typeof setTimeout> | undefined;
  return function (...args: any[]) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      func(...args);
    }, wait);
  };
}

const updateFilteredList = debounce(() => {
  filteredList.value = items.value.filter(item =>
    item.toLowerCase().startsWith(searchQuery.value.toLowerCase())
  );
}, 500);

watch(searchQuery, () => {
  updateFilteredList();
});

const handleBlur = () => {
  searchQuery.value = '';
};

const highlightMatch = (item: string): string => {
  if (!searchQuery.value) return item;

  const regex = new RegExp(`^(${searchQuery.value})`, 'i');
  return item.replace(regex, '<b>$1</b>');
};

const selectItem = (item: string) => {
  searchQuery.value = item;
};
</script>

<style lang="scss" scoped>

$border-color-focus: #007bff;
$width-container: 450px;
$font-size-container: 22px;
$min-height-dropdown: 180px;
$max-height-dropdown: 400px;
$height-input: 45px;
$height-input-icon: $height-input * 0.5;
$margin-li: 13px;
$font-allelement: sans-serif;

.search-container {
  max-width: $width-container;
  position: relative;
  margin-left: 16px;
  margin-right: 16px;
  margin-top: 24px;
  margin-bottom: 24px;

  input[type="text"] {
    position: relative;
    font-family: $font-allelement;
    width: 100%;
    height: $height-input;
    font-size: $font-size-container;
    border: 1.5px solid #9f979773;
    border-radius: 8px;
    outline: none;
    transition: border-color 0.2s;
    padding-left: 10px;
    padding-right: 40px; 
    box-sizing: border-box; 
   

    &:focus, &:valid {
      border-color: $border-color-focus;

      & + .placeholder  {
        top: -1px;
        color: $border-color-focus;
        background-color: white;
        font-size: 17px;
      }
    }

    &.has-dropdown {
      border-bottom: none;
      border-radius: 8px 8px 0 0;
      border-color: $border-color-focus;
    }
  }

  .placeholder {
    position: absolute;
    left: 10px;
    top: 50%;
    font-size: $font-size-container;
    padding: 3px;
    transform: translateY(-50%);
    transition: 0.8ms ease;
    transform-origin: left top;
    pointer-events: none;
    overflow: hidden;
    text-overflow: ellipsis;
    width: min-content;
    color: #9f979773;
    font-family: $font-allelement;
    white-space: nowrap;
  }
  
  .iconPositionDown {
    position: absolute;
      right: 15px;
      top: $height-input-icon;
      transform: translateY(-65%) rotate(180deg);
      font-size: 25px;
      font-weight: lighter;
      color: #0a00007d;
  }
  .iconPositionTop {
      position: absolute;
      right: 15px;
      top: $height-input-icon;
      transform: translateY(-35%);
      font-size: 25px;
      font-weight: lighter;
      color: #0a0000c2;
    
  }

  .dropdown {
    background-color: white;
    border: 1.5px solid $border-color-focus;
    border-radius: 0 0 4px 4px;
    min-height: $min-height-dropdown;
    max-height: $max-height-dropdown;
    overflow-y: auto;
    max-width: $width-container;
    position: relative;
    border-top: none;
    top:100%;

    .dropdown-list {
      list-style: none;
      padding: 0;
      margin: 0;

      li {
        margin: $margin-li;
        cursor: pointer;
        text-align: start;
        background-color: #dcb9fa;
        position: relative;
        font-size: $font-size-container;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        font-family: $font-allelement;

        &:hover {
          background-color: #9f979773;
        }

        b {
          font-weight: bold;
        }
      }
    }
  }
}
</style>