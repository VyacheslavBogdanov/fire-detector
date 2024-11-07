<template>
  <div id="app">
    <DatePicker />
    <Search />
    <DropdownWithSearch />
    <CheckboxFilter />
  </div>
</template>

<script setup lang="ts">

import DatePicker from './components/DatePicker.vue';
import Search from './components/Search.vue';
import DropdownWithSearch from './components/DropdownWithSearch.vue';
import CheckboxFilter from './components/CheckboxFilter.vue';

</script>

<style lang="scss" scoped>
// #app {
//   display: flex;
//   flex-direction: row;
//   align-items: center;
// }
</style>
