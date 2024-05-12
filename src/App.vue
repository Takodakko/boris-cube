<script setup lang="ts">
  import { ref, StyleValue } from 'vue';
  import Square from './components/Square.vue'

  function assignColors() {
    const listOfColors: Array<string> = ['red', 'blue', 'yellow', 'white', 'green', 'purple'];
    const listOfSelectedColors: string[][] = [];
    let repeat = 5;
    while (repeat >= 0) {
      const newColorSet: string[] = new Array(9);
      newColorSet.fill(listOfColors[repeat]);
      listOfSelectedColors.push(newColorSet);
      repeat -= 1;
    }
    
    const newCube: Record<string, string[]> = {
      f: [...listOfSelectedColors[0]],
      b: [...listOfSelectedColors[1]],
      l: [...listOfSelectedColors[2]],
      r: [...listOfSelectedColors[3]],
      u: [...listOfSelectedColors[4]],
      d: [...listOfSelectedColors[5]],
    };
    return newCube;
  }

  const initialGameState = assignColors();
  const currentGameState = ref(initialGameState);

  function rotateRow(arrow: [string[], number[], string[]]) {
    const cube = {...currentGameState.value};
    const spillOver: string[] = [];
    
      const [num1, num2, num3] = arrow[1];
      const [side1, side2, side3, side4] = arrow[0];
      const rotatingFaceDirections = arrow[2];
      
      if (rotatingFaceDirections[0] !== 'skip') {
        const face = rotatingFaceDirections[0];
        const saveEven = cube[face][0];
        const saveOdd = cube[face][1];
        if (rotatingFaceDirections[1] === 'spinClockwise') {
          cube[face][0] = cube[face][6];
          cube[face][6] = cube[face][8];
          cube[face][8] = cube[face][2];
          cube[face][2] = saveEven;
          cube[face][1] = cube[face][3];
          cube[face][3] = cube[face][7];
          cube[face][7] = cube[face][5];
          cube[face][5] = saveOdd;
        } else {
          cube[face][0] = cube[face][2];
          cube[face][2] = cube[face][8];
          cube[face][8] = cube[face][6];
          cube[face][6] = saveEven;
          cube[face][1] = cube[face][5];
          cube[face][5] = cube[face][7];
          cube[face][7] = cube[face][3];
          cube[face][3] = saveOdd;
        }
      }

      spillOver.push(cube[side1][num1], cube[side1][num2], cube[side1][num3]);
      
      cube[side1][num1] = cube[side2][num1];
      cube[side1][num2] = cube[side2][num2];
      cube[side1][num3] = cube[side2][num3];

      cube[side2][num1] = cube[side3][num1];
      cube[side2][num2] = cube[side3][num2];
      cube[side2][num3] = cube[side3][num3];

      cube[side3][num1] = cube[side4][num1];
      cube[side3][num2] = cube[side4][num2];
      cube[side3][num3] = cube[side4][num3];

      cube[side4][num1] = spillOver[0];
      cube[side4][num2] = spillOver[1];
      cube[side4][num3] = spillOver[2];

      currentGameState.value = {...cube};
    
  };

  const top = ['f', 'd', 'b', 'u'];
  const bottom = ['f', 'u', 'b', 'd'];
  const hidari = ['f', 'r', 'b', 'l'];
  const migi = ['f', 'l', 'b', 'r'];

  const left = [0, 3, 6];
  const mid = [1, 4, 7];
  const right = [2, 5, 8];
  const ue = [0, 1, 2];
  const naka = [3, 4, 5];
  const shita = [6, 7, 8];
  
  const leftSpinUp: string[] = ['l', 'spinCounterClockwise'];
  const leftSpinDown: string[] = ['l', 'spinClockwise'];
  const rightSpinUp: string[] = ['r', 'spinClockwise'];
  const rightSpinDown: string[] = ['r', 'spinCounterClockwise'];
  const topSpinLeft: string[] = ['u', 'spinClockwise'];
  const topSpinRight: string[] = ['u', 'spinCounterClockwise'];
  const bottomSpinLeft: string[] = ['d', 'spinCounterClockwise'];
  const bottomSpinRight: string[] = ['d', 'spinClockwise'];
  const skip: string[] = ['skip', 'skip'];

  const topLeft: [string[], number[], string[]] = [top, left, leftSpinUp];
  const topMid: [string[], number[], string[]] = [top, mid, skip];
  const topRight: [string[], number[], string[]] = [top, right, rightSpinUp];

  const rightTop: [string[], number[], string[]] = [migi, ue, topSpinRight];
  const rightMid: [string[], number[], string[]] = [migi, naka, skip];
  const rightBottom: [string[], number[], string[]] = [migi, shita, bottomSpinRight];

  const bottomLeft: [string[], number[], string[]] = [bottom, left, leftSpinDown];
  const bottomMid: [string[], number[], string[]] = [bottom, mid, skip];
  const bottomRight: [string[], number[], string[]] = [bottom, right, rightSpinDown];

  const leftTop: [string[], number[], string[]] = [hidari, ue, topSpinLeft];
  const leftMid: [string[], number[], string[]] = [hidari, naka, skip];
  const leftBottom: [string[], number[], string[]] = [hidari, shita, bottomSpinLeft];

  const arrowSet = [
  topLeft,
  topMid,
  topRight,
  leftTop,
  leftMid,
  leftBottom,
  rightBottom,
  rightMid,
  rightTop,
  bottomLeft,
  bottomMid,
  bottomRight,
  ];

  function scramble() {
    let repeat = 20;
    while (repeat > 0) {
      const randomNumber = Math.floor(Math.random() * arrowSet.length);
      rotateRow(arrowSet[randomNumber]);
      repeat -= 1;
    }
  };

  const cubeFaceStyling: StyleValue = {
    'display': 'flex', 
    'flex-direction': 'row', 
    'border-color': 'black', 
    'border': 'solid', 
    'border-width': '2px', 
    'padding': '1px', 
    'margin': '1px',
    'flex-wrap': 'wrap',
    'width': '205px'
  };
</script>

<template>
  <button @click="scramble">Scramble</button><br>
  <button @click="rotateRow(topLeft)">Spin Left Up</button>
  <button @click="rotateRow(topMid)">Spin Middle Up</button>
  <button @click="rotateRow(topRight)">Spin Right Up</button>
  <br>
  <button @click="rotateRow(leftTop)">Spin Top to Left</button>
  <button @click="rotateRow(leftMid)">Spin Middle to Left</button>
  <button @click="rotateRow(leftBottom)">Spin Bottom to Left</button>
  <button @click="rotateRow(rightTop)">Spin Top to Right</button>
  <button @click="rotateRow(rightMid)">Spin Middle to Right</button>
  <button @click="rotateRow(rightBottom)">Spin Bottom to Right</button>
  <br>
  <button @click="rotateRow(bottomLeft)">Spin Left Down</button>
  <button @click="rotateRow(bottomMid)">Spin Middle Down</button>
  <button @click="rotateRow(bottomRight)">Spin Right Down</button>
  <br>
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  UP
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.u" :color="color" :key="'u-' + index"/><br>
  </div>
</div>
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  LEFT
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.l" :color="color" :key="'l-' + index"/><br>
  </div>
  FRONT
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.f" :color="color" :key="'f-' + index"/><br>
  </div>
  RIGHT
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.r" :color="color" :key="'r-' + index"/>
  </div>
</div>
<div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  DOWN
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.d" :color="color" :key="'d-' + index"/><br>
  </div>
</div>
<div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  BACK
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.b" :color="color" :key="'b-' + index"/><br>
  </div>
</div>
</template>

<style scoped>
  
</style>
