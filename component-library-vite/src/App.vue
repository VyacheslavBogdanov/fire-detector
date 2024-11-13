<template>
	<div id="app">
		<Search />
		<DropdownWithSearch />
		<CheckboxFilter />
		<RadioButtonFilter />
		<Chips />
		<ChipsFilter />
		<DatePicker />
		<Informer />
	</div>
</template>

<script setup lang="ts">
import Search from '@/components/Search/Search.vue';
import DropdownWithSearch from '@/components/DropdownWithSearch/DropdownWithSearch.vue';
import CheckboxFilter from '@/components/CheckboxFilter/CheckboxFilter.vue';
import RadioButtonFilter from '@/components/RadioButtonFilter/RadioButtonFilter.vue';
import Chips from '@/components/Chips/Chips.vue';
import ChipsFilter from '@/components/ChipsFilter/ChipsFilter.vue';
import DatePicker from '@/components/DatePicker/DatePicker.vue';
import Informer from '@/components/Informer/Informer.vue';
</script>

<style lang="scss" scoped></style>
