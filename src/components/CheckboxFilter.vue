<template>
  <h1>Checkbox</h1>
  <div class="filter-container">
    <label :class="{ 'filter-label': true, 'active': isDropdownVisible }">Исполнитель</label>
    <input
      readonly
      type="text"
      v-model="displayText"
      @focus="isDropdownVisible = true"
      @blur="handleBlur"
      :class="{'filter-input': true, 'has-dropdown': isDropdownVisible }"
      :disabled="noResultsFound"
    />
    <div v-if="isDropdownVisible" class="iconPositionTop">⌃</div>
    <div v-else class="iconPositionDown">⌃</div>

    <div v-if="isDropdownVisible" class="dropdown">
      <div class="search-wrapper" v-if="showSearch">
        <input class="search" type="text" v-model="searchQuery" placeholder="Поиск" />
        <span class="searchicon"></span>
      </div>

      <ul class="dropdown-list">
        <li>
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
</template>

<script lang="ts">
import { defineComponent, ref, computed, watch } from 'vue';

function debounce<T extends (...args: any[]) => void>(func: T, wait: number): (...args: Parameters<T>) => void {
  let timeout: ReturnType<typeof setTimeout> | undefined;
  return function (...args: any[]) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      func(...args);
    }, wait);
  };
}

export default defineComponent({
  setup() {
    const isDropdownVisible = ref<boolean>(false);
    const searchQuery = ref<string>('');
    const selectAll = ref<boolean>(false);
    const checkedItems = ref<string[]>([]);
    const items = ref<string[]>(Array(11).fill('hello')); // Заполните массив вашими данными.
    const noResultsFound = ref<boolean>(false); 

    const filteredList = ref<string[]>(items.value); // Изначально равен всем элементам

    const updateFilteredList = debounce(() => {
      filteredList.value = items.value.filter(item =>
        item.toLowerCase().startsWith(searchQuery.value.toLowerCase())
      );

      noResultsFound.value = filteredList.value.length === 0; // Устанавливаем, если ничего не найдено
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

    const handleBlur = (event: FocusEvent) => {
      const relatedTarget = event.relatedTarget as HTMLElement;

      if (relatedTarget && relatedTarget.closest('.dropdown')) {
        return; 
      }

      isDropdownVisible.value = false;
    };

    const handleSelectAll = () => {
      if (selectAll.value) {
        checkedItems.value = [...filteredList.value]; // Взять из отфильтрованного списка
      } else {
        checkedItems.value = [];
      }
    };

    const updateDisplayText = () => {
      selectAll.value = checkedItems.value.length === filteredList.value.length;
    };

    const itemsToDisplay = computed(() => {
      return filteredList.value;
    });

    const showSearch = computed(() => items.value.length > 10);

    return {
      isDropdownVisible,
      searchQuery,
      selectAll,
      checkedItems,
      displayText,
      highlightMatch,
      itemsToDisplay,
      showSearch,
      handleBlur,
      handleSelectAll,
      updateDisplayText,
      noResultsFound,
    };
  },
});
</script>






<style lang="scss" scoped>

$width-checkbox: 400px;
$height-input: 45px;
$focus-color: #007bff;

.filter-container {
  position: static;
  width: $width-checkbox;
  margin-left: 16px;
  margin-right: 16px;
  margin-top: 24px;
  margin-bottom: 24px;
  display: flex;
  flex-direction: column;

  .filter-label {
    position: relative;
    width: 90px;
    height: 20px;
    top: 10px;
    left: 15px;
    background-color: white;
    padding: 0 5px;
    font-size: 16px;
    color: #00000094;

    &.active {
      color: $focus-color;
    }
  }
  .iconPositionDown {
      position: relative;
      
      width: 40px;
      height: 40px;
      top: -20px;
      margin-left: auto;
      transform: translateY(-65%) rotate(180deg);
      font-size: 33px;
      color: #9f979773;
  }
  .iconPositionTop {
      position: relative;
      margin-left: auto;
      width: 40px;
      height: 40px;
      top: -20px;
      transform: translateY(-35%);
      font-size: 33px;
      color: #0a0000c2;
    
  }

  .filter-input {
    width: 100%;
    padding-left: 10px;
    font-size: 16px;
    border: 1.5px solid #cccccc73;
    border-radius: 8px;
    outline: none;
    transition: border-color 0.2s;
    cursor: pointer;
    box-sizing: border-box;
    height: $height-input;
    
    

      &.has-dropdown {
        border-bottom: none;
        border-radius: 4px 4px 0 0;
        border-color: $focus-color;
      }
    }

    
    
    
    
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
      width: 100%;
    }

    .search-wrapper {
    position: relative;
    width: 95%;
    margin: auto;
}

.search {
    width: 100%; 
    height: $height-input;
    border: 1.5px solid #cccccc73;
    border-radius: 4px;
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
        border: 1.5px solid #cccccc73; 
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
        background: #cccccc73;
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
} 



</style>