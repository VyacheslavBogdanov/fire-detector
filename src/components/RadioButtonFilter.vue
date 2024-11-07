<template>
    <h1>RadioButton Фильтр</h1>
    <div class="filter-container" ref="filterContainer">
      <label :class="{ 'filter-label': true, 'active': isDropdownVisible }">Исполнитель</label>
      <input
        readonly
        type="text"
        v-model="displayText"
        @focus="isDropdownVisible = true"
        :class="{ 'filter-input': true, 'has-dropdown': isDropdownVisible }"
      />
      <div :class="{ 'iconPositionTop': isDropdownVisible, 'iconPositionDown': !isDropdownVisible }">⌃</div>
      <div
        v-if="isDropdownVisible"
        class="dropdown"
        @mousedown="handleDropdownClick"
      >
        <div class="search-wrapper" v-if="showSearch">
          <input class="search" type="text" v-model="searchQuery" placeholder="Поиск" @focus="isDropdownVisible = true" />
          <span class="searchicon"></span>
        </div>
        <ul class="dropdown-list">
          <li v-if="!noResultsFound">
            <label>
              <input type="radio" name="items" v-model="selectedItem" :value="null" />
              <span>Не выбрано</span>
            </label>
          </li>
          <li v-for="(item, index) in itemsToDisplay" :key="index">
            <label>
              <input type="radio" name="items" v-model="selectedItem" :value="item" />
              <span v-html="highlightMatch(item)"></span>
            </label>
          </li>
        </ul>
        <div v-if="noResultsFound" class="no-results">Результаты не найдены</div>
      </div>
    </div>
  </template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onBeforeUnmount } from 'vue';

function debounce<T extends (...args: any[]) => void>(func: T, wait: number): (...args: Parameters<T>) => void {
  let timeout: ReturnType<typeof setTimeout> | undefined;
  return function (...args: any[]) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      func(...args);
    }, wait);
  };
}

const isDropdownVisible = ref<boolean>(false);
const searchQuery = ref<string>('');
const selectedItem = ref<string | null>(null);
const items = ref<string[]>(["Иванов", "Петров", "Сидоров", "Кузнецов", "Смирнов",
  "Попов", "Васильев", "Михайлов", "Новиков", "Федоров",
  "Кравцов", "Никитин", "Соловьев", "Лебедев", "Семенов",
  "Егоров", "Павлов", "Козлов", "Волков", "Зайцев",
  "Калинин", "Александров", "Сергеев", "Марков", "Коновалов",
  "Макаров", "Андреев", "Ковалев", "Ильин", "Гусев",
  "Титов", "Кузьмин", "Захаров", "Орлов", "Виноградов",
  "Курочкин", "Яковлев", "Голубев", "Баранов", "Максимов",
  "Кабанов", "Фролов", "Осипов", "Мясников", "Кириллов",
  "Маклаков", "Лазарев", "Мельников", "Ершов", "Николаев",
  "Кочетов", "Зимин", "Савельев", "Миронов", "Князев",
  "Петровский", "Беляев", "Тарасов", "Борисов", "Никифоров",
  "Романов", "Логинов", "Померанцев", "Гончаров", "Евдокимов",
  "Климов", "Камалетдинов", "Фомичев", "Богданов", "Васильев",
  "Устинов", "Лыткин", "Демьянов", "Морозов", "Герасимов",
  "Терентьев", "Беспалов", "Фомин", "Давыдов", "Рябов",
  "Гришин", "Горшков", "Заикин", "Платонов", "Гаврилов",
  "Голованов", "Емельянов", "Карпов", "Сазонов", "Авдеев",
  "Суханов", "Котов", "Жуков", "Медведев", "Токарев"
].sort());
const filteredList = ref<string[]>(items.value);
const noResultsFound = ref<boolean>(false);

const updateFilteredList = debounce(() => {
  filteredList.value = items.value.filter(item => 
    item.toLowerCase().startsWith(searchQuery.value.toLowerCase())
  );
  noResultsFound.value = filteredList.value.length === 0;

  
  if (selectedItem.value && !filteredList.value.includes(selectedItem.value)) {
    selectedItem.value = null; 
  }
}, 700);

watch(searchQuery, () => {
  updateFilteredList();
});

const displayText = computed(() => {
  return selectedItem.value !== null ? selectedItem.value : 'Не выбрано';
});

const highlightMatch = (item: string): string => {
  if (!searchQuery.value) return item;
  const regex = new RegExp(`^(${searchQuery.value})`, 'i');
  return item.replace(regex, '<b>$1</b>');
};

const filterContainer = ref<HTMLElement | null>(null);
const handleClickOutside = (event: MouseEvent) => {
  if (filterContainer.value && !filterContainer.value.contains(event.target as Node)) {
    isDropdownVisible.value = false;
  }
};

onMounted(() => {
  document.addEventListener('mousedown', handleClickOutside);
});

onBeforeUnmount(() => {
  document.removeEventListener('mousedown', handleClickOutside);
});

const handleDropdownClick = (event: MouseEvent) => {
  const target = event.target as HTMLElement;
  if (!target.classList.contains('search')) {
    event.preventDefault();
  }
};

const itemsToDisplay = computed(() => {
  return filteredList.value;
});

const showSearch = computed(() => items.value.length > 10);
</script>

<style lang="scss" scoped>

$width-checkbox:450px;
$height-input: 45px;
$focus-color: #007bff;
$font-allelement: sans-serif;


.filter-container {
position: relative;
width: $width-checkbox;
margin-left: 16px;
margin-right: 16px;
margin-top: 24px;
margin-bottom: 24px;
display: flex;
flex-direction: column;

.iconPositionDown {
    position: absolute;
      right: 15px;
      top: 44.5px;
      transform: translateY(-65%) rotate(180deg);
      font-size: 25px;
      font-weight: lighter;
      color: #0a00007d;
  }
  .iconPositionTop {
      position: absolute;
      right: 15px;
      top: 44.5px;
      transform: translateY(-35%);
      font-size: 25px;
      font-weight: lighter;
      color: #0a0000c2;
    
  }

.filter-label {
position: relative;
width: min-content;
height: 20px;
top: 10px;
left: 15px;
background-color: white;
padding: 0 3px;
font-size: 16px;
color: #00000094;
font-family: $font-allelement;

&.active {
  color: $focus-color;
}
}

}

.filter-input {
width: 100%;
padding-left: 10px;
font-size: 16px;
color: #00000094;
border: 1.5px solid #9f979773;
border-radius: 8px;
outline: none;
transition: border-color 0.2s;
cursor: pointer;
box-sizing: border-box;
height: $height-input;
padding-right: 40px; 

&.has-dropdown {
  border-bottom: none;
  border-radius: 8px 8px 0 0;
  border-color: $focus-color;
  
}
 
}

.dropdown {
position: relative;
background-color: white;
border: 1.5px solid $focus-color;
border-radius: 0 0 8px 8px;
min-height: 180px;
max-height: 400px;
overflow-y: auto;
width: 100%;
transition: border-color 0.2s, box-shadow 0.2s;
border-top: #ffffff;
box-sizing: border-box;
display: flex;
flex-direction: column;




.search-wrapper {
position: relative;
width: 95%;
margin: 7px 0 0 10px;
}

.search {
width: 100%; 
height: $height-input;
border: 1.5px solid #9f979773;
border-radius: 8px;
outline: none;
padding-right: 40px; 
padding-left: 10px;
box-sizing: border-box;
font-size: 16px ;

&:focus {
    border-color: $focus-color;

    ~ .searchicon {
        &::before {
            border-color: $focus-color;
        }
        &::after {
            background: $focus-color;
        }
    }
}
}

.searchicon {
position: absolute;
right: 10px; 
top: 50%;
transform: translateY(-50%);
width: 30px;
height: 30px;
display: grid;
place-items: center;
pointer-events: none;

&::before {
    content: '';
    width: 9.5px;
    height: 9.5px;
    border: 1.5px solid #9f979773; 
    border-radius: 50%;
    transition: border-color 0.2s;
    position: absolute;
    transform: translate(-2px, -2px);
}

&::after {
    content: '';
    position: absolute;
    width: 1.5px;
    height: 9.5px;
    background: #9f979773;
    transition: border-color 0.2s;
    transform: rotate(-45deg) translate(-0px, 7px);
}
}

.dropdown-list {
list-style: none;
padding: 0;
margin: 0;
width: 100%;


li {
padding: 8px;
margin: 0;
cursor: pointer;
text-align: start;
position: relative;
&:hover {
      background-color: #ece7e773;
    }
}

b {
  font-weight: bold;
}
}
.no-results {
color:#00000094;
position: relative;
margin: auto;
font-size: 16px;
font-family: $font-allelement;

}
} 

</style>