<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  color: {type: String, required: true}
});
// const { color } = props;
const color = computed(() => props.color);
const squareStyling = {
    'border-color': 'black', 
    'border': 'solid', 
    'border-width': '2px', 
    'padding': '1px', 
    'margin': '1px',
    'background-color': color.value, 
    'height': '60px', 
    'width': '60px', 
    'color': 'black'
}
</script>

<template>
  <div :style="squareStyling">
    {{props.color}}
  </div>
</template>

<style scoped>
  
</style>
