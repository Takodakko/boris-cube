<script setup lang="ts">
  import { ref, StyleValue } from 'vue';
  import Square from './components/Square.vue'

  interface IArrow extends Array<string[] | number[]> {
  0: Array<cubeFaceLabels>; 
  1: number[]; 
  2: IRotatingFaceInstructions;
  }

  type cubeFaceLabels = 'f' | 'b' | 'l' | 'r' | 'u' | 'd';

  interface IRotatingFaceInstructions extends Array<string> {
    0: cubeFaceLabels | 'skip';
    1: 'spinClockwise' | 'spinCounterClockwise' | 'skip';
  }

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
    
    const newCube: Record<cubeFaceLabels, string[]> = {
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

  function rotateRow(arrow: IArrow) {
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

  const top: cubeFaceLabels[] = ['f', 'd', 'b', 'u'];
  const bottom: cubeFaceLabels[] = ['f', 'u', 'b', 'd'];
  const hidari: cubeFaceLabels[] = ['f', 'r', 'b', 'l'];
  const migi: cubeFaceLabels[] = ['f', 'l', 'b', 'r'];

  const left = [0, 3, 6];
  const mid = [1, 4, 7];
  const right = [2, 5, 8];
  const ue = [0, 1, 2];
  const naka = [3, 4, 5];
  const shita = [6, 7, 8];
  
  const leftSpinUp: IRotatingFaceInstructions = ['l', 'spinCounterClockwise'];
  const leftSpinDown: IRotatingFaceInstructions = ['l', 'spinClockwise'];
  const rightSpinUp: IRotatingFaceInstructions = ['r', 'spinClockwise'];
  const rightSpinDown: IRotatingFaceInstructions = ['r', 'spinCounterClockwise'];
  const topSpinLeft: IRotatingFaceInstructions = ['u', 'spinClockwise'];
  const topSpinRight: IRotatingFaceInstructions = ['u', 'spinCounterClockwise'];
  const bottomSpinLeft: IRotatingFaceInstructions = ['d', 'spinCounterClockwise'];
  const bottomSpinRight: IRotatingFaceInstructions = ['d', 'spinClockwise'];
  const skip: IRotatingFaceInstructions = ['skip', 'skip'];

  const topLeft: IArrow = [top, left, leftSpinUp];
  const topMid: IArrow = [top, mid, skip];
  const topRight: IArrow = [top, right, rightSpinUp];

  const rightTop: IArrow = [migi, ue, topSpinRight];
  const rightMid: IArrow = [migi, naka, skip];
  const rightBottom: IArrow = [migi, shita, bottomSpinRight];

  const bottomLeft: IArrow = [bottom, left, leftSpinDown];
  const bottomMid: IArrow = [bottom, mid, skip];
  const bottomRight: IArrow = [bottom, right, rightSpinDown];

  const leftTop: IArrow = [hidari, ue, topSpinLeft];
  const leftMid: IArrow = [hidari, naka, skip];
  const leftBottom: IArrow = [hidari, shita, bottomSpinLeft];

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

  function turnWholeCube(arrow1: IArrow, arrow2: IArrow, arrow3: IArrow) {
    rotateRow(arrow1);
    rotateRow(arrow2);
    rotateRow(arrow3);
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

  const frontCubeFaceStyling: StyleValue = {
    'display': 'flex', 
    'flex-direction': 'row', 
    'border-color': 'black', 
    'border': 'solid', 
    'border-width': '2px', 
    'padding': '1px', 
    'margin': '1px',
    'flex-wrap': 'wrap',
    'width': '265px'
  };

</script>

<template>
  <button @click="scramble">Scramble</button><br>
  FRONT<br>
  <button @click="turnWholeCube(topLeft, topMid, topRight)">SPIN UP</button><br>
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
    <button @click="rotateRow(topLeft)">^</button>
    <div :style="{'width': '60px'}"></div>
    <button @click="rotateRow(topMid)">^</button>
    <div :style="{'width': '60px'}"></div>
    <button @click="rotateRow(topRight)">^</button>
    <br>
  </div>
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
    <button @click="turnWholeCube(leftTop, leftMid, leftBottom)">SPIN LEFT</button><br>
    <div :style="{display: 'flex', 'flex-direction': 'column'}">
      <br>
      <button @click="rotateRow(leftTop)"><</button><br><br>
      <button @click="rotateRow(leftMid)"><</button><br><br>
      <button @click="rotateRow(leftBottom)"><</button>
    </div>
      <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center', 'justify-items': 'center'}">
      <div :style="frontCubeFaceStyling">
        <Square v-for="(color, index) in currentGameState.f" :color="color" :key="'f-' + index" :is-on-front="true"/><br>
      </div>
    </div>
    <div :style="{display: 'flex', 'flex-direction': 'column'}">
      <br>
      <button @click="rotateRow(rightTop)">></button><br><br>
      <button @click="rotateRow(rightMid)">></button><br><br>
      <button @click="rotateRow(rightBottom)">></button>
    </div>
    <button @click="turnWholeCube(rightTop, rightMid, rightBottom)">SPIN RIGHT</button><br>
  </div>
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
    <button @click="rotateRow(bottomLeft)">v</button>
    <div :style="{'width': '60px'}"></div>
    <button @click="rotateRow(bottomMid)">v</button>
    <div :style="{'width': '60px'}"></div>
    <button @click="rotateRow(bottomRight)">v</button>
  </div>
  <button @click="turnWholeCube(bottomLeft, bottomMid, bottomRight)">SPIN DOWN</button><br>
  <br>
  <br>
  UP
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center', 'justify-items': 'center'}">
    <div :style="cubeFaceStyling">
      <Square v-for="(color, index) in currentGameState.u" :color="color" :key="'u-' + index" :is-on-front="false"/><br>
    </div>
  </div>
  
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
    LEFT
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.l" :color="color" :key="'l-' + index" :is-on-front="false"/><br>
  </div>
  
  RIGHT
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.r" :color="color" :key="'r-' + index" :is-on-front="false"/>
  </div>
</div>
<div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  DOWN
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.d" :color="color" :key="'d-' + index" :is-on-front="false"/><br>
  </div>
</div>
<div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  BACK
  <div :style="cubeFaceStyling">
    <Square v-for="(color, index) in currentGameState.b" :color="color" :key="'b-' + index" :is-on-front="false"/><br>
  </div>
</div>
</template>

<style scoped>
  
</style>
