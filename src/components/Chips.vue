<template>
    <h1>Chips</h1>
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
          <li v-if="itemsToDisplay.length > 0">
            <label>
              <input type="checkbox" v-model="selectAll" @change="handleSelectAll">
              <span>Все</span>
            </label>
          </li>
          <li v-for="(item, index) in itemsToDisplay" :key="index">
            <label>
              <input type="checkbox" v-model="checkedItems" :value="item" @change="updateDisplayText" />
              <span v-html="highlightMatch(item)"></span>
            </label>
          </li>
        </ul>
        <div v-if="noResultsFound" class="no-results">Результаты не найдены</div>
      </div>
    </div>
    <div v-if="!isDropdownVisible" class="chips-container">
      <div v-for="(chip, index) in checkedItems" :key="index" class="chip">
       <!-- :title="chip.length > 15 ? chip : ''"> -->
        <span class="chip-content">{{ chip }}</span>
        <span v-if="tooltipVisible" class="tooltip">{{ chip }}</span>
        <button class="delete-chip" @click="removeChip(chip)"><div class="delete-chip-icon">✕</div></button>
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
  
  const tooltipVisible = ref<boolean>(true);
  const isDropdownVisible = ref<boolean>(false);
  const searchQuery = ref<string>('');
  const selectAll = ref<boolean>(false);
  const checkedItems = ref<string[]>([]);
  const items = ref<string[]>([
    "1234567890123456789012345", "Ивановвввввввввввввввввввв Д.С.", "Кузнецоввввввввввввввввв Е.М.", "Попов Н.А.", "Лебедев И.П.", "Козлов В.Н.",
    "Новиков А.Д.", "Морозов С.А.", "Петров Е.В.", "Васильев А.С.", "Соколов В.И.", "Михайлов О.В.",
    "Фёдоров Д.Л.", "Орлов И.К.", "Волков А.А.", "Андреев П.С.", "Никитин О.В.", "Захаров А.И.", "Куликов Д.П.",
    "Александровв С.В.", "Дмитриев В.Н.", "Ковалёв Е.М.", "Ситников Л.П.", "Григорьев В.Д.", "Гордеев А.С.", "Антонов И.Н.",
    "Ефимов В.П.", "Тимофеев Д.В.", "Филиппов Е.С.", "Макаров О.А.", "Сидоров В.Д.", "Чернов И.П.", "Савельев Н.В.", "Павлов А.С.",
    "Богданов С.К.", "Мартынов Е.В.", "Воробьёв А.М.", "Антипов Д.А.", "Тарасов В.О.", "Беляев Л.В.", "Комаров И.С.", "Мельников Е.К.",
    "Шевченко С.В.", "Емельянов О.П.", "Князев В.А.", "Белов Е.И.", "Щербаков С.Д.", "Назаров Д.В.", "Кочетов О.С.", "Афанасьев Н.А."
  ].sort());
  const noResultsFound = ref<boolean>(false);
  const filteredList = ref<string[]>(items.value);
  
  const updateFilteredList = debounce(() => {
    filteredList.value = items.value.filter(item =>
      item.toLowerCase().startsWith(searchQuery.value.toLowerCase())
    );
  
    noResultsFound.value = filteredList.value.length === 0;
  }, 700);
  
  watch(searchQuery, () => {
    updateFilteredList();
  });
  
  const displayText = computed(() => {
    if (checkedItems.value.length === 0) return 'Не выбрано';
    if (checkedItems.value.length === 1) return checkedItems.value[0];
    return `Выбрано ${checkedItems.value.length}`;
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
  
  const handleSelectAll = () => {
    if (selectAll.value) {
      checkedItems.value = [...filteredList.value];
    } else {
      checkedItems.value = [];
    }
  };
  
  const updateDisplayText = () => {
    selectAll.value = checkedItems.value.length === filteredList.value.length;
  };
  
  const itemsToDisplay = computed(() => {
    if (filteredList.value.length <= 10) return filteredList.value;
  
    const selectedItems = filteredList.value
      .filter(item => checkedItems.value.includes(item))
      .sort((a, b) => a.localeCompare(b));
  
    const unselectedItems = filteredList.value
      .filter(item => !checkedItems.value.includes(item));
  
    return [...selectedItems, ...unselectedItems];
  });
  
  const showSearch = computed(() => items.value.length > 10);
  
  const removeChip = (chip: string) => {
    checkedItems.value = checkedItems.value.filter(item => item !== chip);
    updateDisplayText();
  };
  </script>

  <style lang="scss" scoped>
  $height-input: 45px;
  $focus-color: #007bff;
  $font-allelement: sans-serif;
  $border-color: #9f979773;
  $icon-color: #0a00007d;
  $icon-color-active: #0a0000c2;
  $hover-background: #ece7e773;
  $text-color: #00000094;
  $font-size: 16px;
  $cursor: pointer;
  $chip-bg-color: #e0e0e0;
  $chip-text-color: #333;
  $chip-hover-bg-color: #d0d0d0;
  $chip-border-radius: 16px;
  
 
.filter-container {
  position: relative;
  width: 450px;
  margin: 24px 16px;
  display: flex;
  flex-direction: column;

  .iconPositionDown {
    position: absolute;
    right: 15px;
    top: 44.5px;
    transform: translateY(-65%) rotate(180deg);
    font-size: 25px;
    font-weight: lighter;
    color: $icon-color;
    cursor: $cursor;
  }
  .iconPositionTop {
    position: absolute;
    right: 15px;
    top: 44.5px;
    transform: translateY(-35%);
    font-size: 25px;
    font-weight: lighter;
    color: $icon-color-active;
    cursor: $cursor;
  }

  .filter-label {
    position: relative;
    width: min-content;
    height: 20px;
    top: 10px;
    left: 15px;
    background-color: white;
    padding: 0 3px;
    font-size: $font-size;
    color: $text-color;
    font-family: $font-allelement;

    &.active {
      color: $focus-color;
    }
  }
}

.filter-input {
  width: 100%;
  padding-left: 10px;
  font-size: $font-size;
  color: $text-color;
  border: 1.5px solid $border-color;
  border-radius: 8px;
  outline: none;
  transition: border-color 0.2s;
  cursor: $cursor;
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
    border: 1.5px solid $border-color;
    border-radius: 8px;
    outline: none;
    padding-right: 40px;
    padding-left: 10px;
    box-sizing: border-box;
    font-size: $font-size;

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
      border: 1.5px solid $border-color;
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
      background: $border-color;
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
      cursor: $cursor;
      text-align: start;
      position: relative;

      &:hover {
        background-color: $hover-background;
      }

      label {
      cursor: $cursor;

      input {
      cursor: $cursor;

      }
      }
    }

    b {
      font-weight: bold;
    }
  }

  .no-results {
    color: $text-color;
    position: relative;
    margin: auto;
    font-size: $font-size;
    font-family: $font-allelement;
  }
}

  .chips-container {
    display: flex;
    flex-wrap: wrap;
    max-height: 153px;
    width: 600px;
    overflow-y: auto;
    gap: 8px;
    margin: 12px 0 0 16px;

    .chip {
  position: relative;
  display: flex;
  align-items: center;
  padding: 0 0 0 8px;
  height: 23px;
  max-width: 170px;
  background-color: #e0e0e0;
  border-radius: 50px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-size: 16px;
  color: #333;
  cursor: default;

  &:hover {
    background-color: $chip-hover-bg-color;
  }

  .tooltip {
  display: none;
  position: absolute;
  top: -30px;
  left: 50%;
  transform: translateX(-50%);
  background-color: black;
  color: white;
  font-size: 14px;
  padding: 4px 8px;
  border-radius: 4px;
  white-space: nowrap;
  z-index: 1;
}


  &:hover .tooltip {
    display: block;
  }

  .chip-content {
    overflow: hidden;
    text-overflow: ellipsis;
  }
  
      .delete-chip {
        background: none;
        border: none;
        color: $icon-color;
        font-size: 9px;
        font-weight: bold;
        cursor: pointer;
        margin-left: 8px;

        .delete-chip-icon {
            height: 14px;
            width: 14px;
            border: none;
            border-radius: 50%;
            background-color: rgb(88, 85, 85);
            color: #e0e0e0;
            display: flex;
            align-items: center;
            justify-content: center;
        }
      }
    }
  }
  </style>
  