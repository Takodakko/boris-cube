<script setup lang="ts">

const props = defineProps({
  color: {type: String, required: true},
  isOnFront: {type: Boolean, required: true}
});

const squareStyling = {
    'border-color': 'black', 
    'border': 'solid', 
    'border-width': '2px', 
    'padding': '1px', 
    'margin': '1px',
    'height': '60px', 
    'width': '60px', 
    'color': 'black'
};

const frontSquareStyling = {
    'border-color': 'black', 
    'border': 'solid', 
    'border-width': '2px', 
    'padding': '1px', 
    'margin': '1px',
    'height': '80px', 
    'width': '80px', 
    'color': 'black'
};
</script>

<template>
  <div v-if="isOnFront" :style="{...frontSquareStyling, 'background-color': props.color}">
    {{props.color}}
  </div>
  <div v-else :style="{...squareStyling, 'background-color': props.color}">
    {{props.color}}
  </div>
</template>

<style scoped>
  
</style>
