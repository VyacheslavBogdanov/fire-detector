
<template>
  <h1>Informer</h1>
  <div :class="['informer-container', theme]">
    <button class="ThemeToggle" @click="toggleTheme">
      {{ theme === 'dark' ? 'Light' : 'Dark' }}
    </button>
    <div v-for="type in messageTypes" :key="type.class" :class="['informer', type.class, theme]">
      <!-- <span class="icon">
        <img :src="type.icon" alt="icon" />
      </span>  -->
      
      <div :class="{ 'dark-text': theme === 'dark', 'light-text': theme === 'light' }">
       <div class="icon">ⓘ</div> {{ type.message }}
      </div>
    </div>
  </div>
</template>



<script setup lang="ts">
import { ref } from 'vue';


const messageTypes = [
  { class: 'success', message: 'This is a SUCCESS message' }, //, icon: require ('./img/mssg.svg')
  { class: 'warning', message: 'This is a WARNING message' },
  { class: 'error', message: 'This is an ERROR message' },
  { class: 'info', message: ' This is an INFO message' },
  { class: 'default', message: ' This is a message' },
];

const theme = ref('light');

const toggleTheme = () => {
  theme.value = theme.value === 'light' ? 'dark' : 'light';
};
</script>

<style lang="scss" scoped>

.informer-container {
  padding: 16px;
  margin: 16px;
  display: flex; 
  border-radius:8px;
  flex-direction: column; 
  gap: 30px; 
  transition: background-color 0.3s, color 0.3s; 
  width: 400px;
  

  &.light {
    background-color: #fff; 
    color: #545252; 
  }

  &.dark {
    background-color: #484747; 
    color: #fff; 
  }
}

.ThemeToggle {
  width: 50px; 
  background-color: transparent;
  border: 1.5px solid #9f979773; 
  color: inherit; 
  border-radius: 8px;
  padding: 4px 8px;
  cursor: pointer;
  font-family: sans-serif;
  text-decoration: none;
  transition: background-color 0.3s, color 0.3s;
}

.informer {
  position: relative;
  display: flex; 
  align-items: center;
  padding: 10px 30px 10px 40px;
  border-radius: 8px;
  font-family: Arial, sans-serif;
  max-width: fit-content; 
  word-wrap: break-word; 
  font-size: 23px;
  font-family: sans-serif;
  height: 40px;
  
  .icon {
  display: flex;
  position: absolute;
  transform:rotate(180deg);
  left: 10px;
  height: 30px;
  }
  
  &.success {
    background-color: #e0fde7;
    color: #2a9b44;
  }
    

    &.dark {
      background-color: #7de17d; 
      color: #1c6e1c; 
    }
  

  &.warning {
    background-color: #f9ebd8;
    color: #d77417;
    

    &.dark {
      background-color: #fcbe91; 
      color: #9b5a17; 
    }
  }

  &.error {
    background-color: #f2dee0;
    color: #db1428;
    

    &.dark {
      background-color: #e58d8d; 
      color: #db1428; 
    }
  }

  &.info {
    background-color: #e3e3ff;
    color: #1052ec;
   

    &.dark {
      background-color: #c3c8f2; 
      color: #1a1fa1;
    }
  }

  &.default {
    background-color: #cecece;
    color: #000;

    &.dark {
      background-color: #a6a6a6; 
      color: #ffffff; 
    }
  }
  
}

</style>