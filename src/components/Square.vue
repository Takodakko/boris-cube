<script setup lang="ts">

const props = defineProps({
  color: {type: String, required: true}
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
}
</script>

<template>
  <div :style="{...squareStyling, 'background-color': props.color}">
    {{props.color}}
  </div>
</template>

<style scoped>
  
</style>
