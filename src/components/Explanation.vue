<script setup lang="ts">
  import { computed, StyleValue } from 'vue';
  const props = defineProps({
    sectionName: {type: String, required: true},
  });
  
  const { sectionName } = props;
  const explanationtext: StyleValue = {
    'z-index': '1000',
    'position': 'absolute',
    'background-color': 'beige',
    'color': 'black',
    'border': 'solid',
    'border-color': 'black',
    'padding': '2px',
  };
  const topValue = computed(() => {
    switch(sectionName) {
        case 'upFace':
            return '100px';
        case 'downFace':
            return '450px';
        case 'backFace':
            return '625px';
        case 'leftFace':
            return '300px';
        case 'rightFace':
            return '300px';
        default:
            return '1px';
    }
  });
  
  const textToDisplay = computed(() => {
    
    switch(sectionName) {
        case 'upFace':
            return 'This is the top of the cube.';
        case 'downFace':
            return 'This is the bottom of the cube.';
        case 'backFace':
            return 'This is the back of the cube.'
        case 'leftFace':
            return 'This is the left side of the cube.'
        case 'rightFace':
            return 'This is the right side of the cube.'
        default:
            return ' ';
    }
  });

</script>
<template>
  <div class="explanationtext" :style="{...explanationtext, top: topValue}">
    {{ textToDisplay }}
  </div>
</template>
<style scoped>

</style>