<template>
  <div id="app">
    <DatePicker />
    <Search />
    <DropdownWithSearch />
    <CheckboxFilter />
    <RadioButtonFilter />
    <Chips />
    <Informer />
  </div>
</template>

<script setup lang="ts">

import DatePicker from './components/DatePicker.vue';
import Search from './components/Search.vue';
import DropdownWithSearch from './components/DropdownWithSearch.vue';
import CheckboxFilter from './components/CheckboxFilter.vue';
import RadioButtonFilter from './components/RadioButtonFilter.vue';
import Chips from './components/Chips.vue';
import Informer from './components/Informer.vue';

</script>

<style lang="scss" scoped>
// #app {
//   display: flex;
//   flex-direction: row;
//   align-items: center;
// }
</style>
