<script setup lang="ts">
  import { ref, StyleValue, onMounted, nextTick, Ref } from 'vue';
  import Square from './components/Square.vue'
  import Explanation from './components/Explanation.vue'

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

  const showExplanation = ref({
    upFace: false,
    downFace: false,
    backFace: false,
    leftFace: false,
    rightFace: false,
  });

  const initialfocus: Ref = ref({});

  onMounted(() => {
    nextTick(() => {
      initialfocus.value.focus();
    });
  });

  function rotateRow(arrow: IArrow) {
    const cube = {...currentGameState.value};
    
      const [num1, num2, num3] = arrow[1];
      const [side1, side2, side3, side4] = arrow[0];
      const rotatingFaceDirections = arrow[2];
      
      if (rotatingFaceDirections[0] !== 'skip') {
        const face = rotatingFaceDirections[0];
        if (rotatingFaceDirections[1] === 'spinClockwise') {
          [cube[face][0], cube[face][6], cube[face][8], cube[face][2], cube[face][1], cube[face][3], cube[face][7], cube[face][5]] = [cube[face][6], cube[face][8], cube[face][2], cube[face][0], cube[face][3], cube[face][7], cube[face][5], cube[face][1]];
        } else {
          [cube[face][0], cube[face][6], cube[face][8], cube[face][2], cube[face][1], cube[face][5], cube[face][7], cube[face][3]] = [cube[face][2], cube[face][0], cube[face][6], cube[face][8], cube[face][5], cube[face][7], cube[face][3], cube[face][1]];
        }
      }
      
      [cube[side1][num1], cube[side1][num2], cube[side1][num3], cube[side2][num1], cube[side2][num2], cube[side2][num3], cube[side3][num1], cube[side3][num2], cube[side3][num3], cube[side4][num1], cube[side4][num2], cube[side4][num3]] = [cube[side2][num1], cube[side2][num2], cube[side2][num3], cube[side3][num1], cube[side3][num2], cube[side3][num3], cube[side4][num1], cube[side4][num2], cube[side4][num3], cube[side1][num1], cube[side1][num2], cube[side1][num3]];

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
    'width': '145px'
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
  <div :style="{display: 'flex', 'flex-direction': 'row'}" @keydown.left="turnWholeCube(leftTop, leftMid, leftBottom)" @keydown.right="turnWholeCube(rightTop, rightMid, rightBottom)" @keydown.up.prevent="turnWholeCube(topLeft, topMid, topRight)" @keydown.prevent.down="turnWholeCube(bottomLeft, bottomMid, bottomRight)">
    <div class="screensection">
      <button ref="initialfocus" @click="scramble">Scramble</button><br>
      FRONT<br>
  
      <button class="spinupdown" @click="turnWholeCube(topLeft, topMid, topRight)">SPIN UP</button><br>
  
      <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
        <button @click="rotateRow(topLeft)">^</button>
        <div :style="{'width': '60px'}"></div>
        <button @click="rotateRow(topMid)">^</button>
        <div :style="{'width': '60px'}"></div>
        <button @click="rotateRow(topRight)">^</button>
        <br>
      </div>

      <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
        <button class="spinleftright" @click="turnWholeCube(leftTop, leftMid, leftBottom)">SPIN LEFT</button><br>
        <div :style="{display: 'flex', 'flex-direction': 'column', 'justify-content': 'space-around'}">
          <button @click="rotateRow(leftTop)"><</button>
          <button @click="rotateRow(leftMid)"><</button>
          <button @click="rotateRow(leftBottom)"><</button>
        </div>
        <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center', 'justify-items': 'center'}">
          <div :style="frontCubeFaceStyling">
            <Square v-for="(color, index) in currentGameState.f" :color="color" :key="'f-' + index" :is-on-front="true"/><br>
          </div>
        </div>

        <div :style="{display: 'flex', 'flex-direction': 'column', 'justify-content': 'space-around'}">
          
          <button @click="rotateRow(rightTop)">></button>
          <button @click="rotateRow(rightMid)">></button>
          <button @click="rotateRow(rightBottom)">></button>
        </div>
        <button class="spinleftright" @click="turnWholeCube(rightTop, rightMid, rightBottom)">SPIN RIGHT</button><br>
      </div>

      <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
        <button @click="rotateRow(bottomLeft)">v</button>
        <div :style="{'width': '60px'}"></div>
        <button @click="rotateRow(bottomMid)">v</button>
        <div :style="{'width': '60px'}"></div>
        <button @click="rotateRow(bottomRight)">v</button>
      </div>
      <button class="spinupdown" @click="turnWholeCube(bottomLeft, bottomMid, bottomRight)">SPIN DOWN</button><br>
    </div>
    <br>
    <br>
    <div class="screensection">
  
      <Explanation :section-name="'upFace'" v-show="showExplanation.upFace" />

      UP
      <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center', 'justify-items': 'center'}">
        <div :style="cubeFaceStyling" @mouseover="showExplanation.upFace = true" @mouseleave="showExplanation.upFace = false">
          <Square v-for="(color, index) in currentGameState.u" :color="color" :key="'u-' + index" :is-on-front="false"/><br>
        </div>
      </div>
      LEFT RIGHT
        <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
          <Explanation :section-name="'leftFace'" v-show="showExplanation.leftFace" />
        <div :style="cubeFaceStyling" @mouseover="showExplanation.leftFace = true" @mouseleave="showExplanation.leftFace = false">
          <Square v-for="(color, index) in currentGameState.l" :color="color" :key="'l-' + index" :is-on-front="false"/><br>
        </div>
  
        <Explanation :section-name="'rightFace'" v-show="showExplanation.rightFace" />
        <div :style="cubeFaceStyling" @mouseover="showExplanation.rightFace = true" @mouseleave="showExplanation.rightFace = false">
          <Square v-for="(color, index) in currentGameState.r" :color="color" :key="'r-' + index" :is-on-front="false"/>
        </div>
      </div>
  DOWN
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  
    <div :style="cubeFaceStyling" @mouseover="showExplanation.downFace = true" @mouseleave="showExplanation.downFace = false">
      <Explanation :section-name="'downFace'" v-show="showExplanation.downFace" />
      <Square v-for="(color, index) in currentGameState.d" :color="color" :key="'d-' + index" :is-on-front="false"/><br>
    </div>
  </div>
  BACK
  <div :style="{display: 'flex', 'flex-direction': 'row', 'justify-content': 'center'}">
  
    <div :style="cubeFaceStyling" @mouseover="showExplanation.backFace = true" @mouseleave="showExplanation.backFace = false">
      <Explanation :section-name="'backFace'" v-show="showExplanation.backFace" />
      <Square v-for="(color, index) in currentGameState.b" :color="color" :key="'b-' + index" :is-on-front="false"/><br>
    </div>
  </div>
</div>
</div>
</template>

<style scoped>
  .spinupdown {
    width: 400px;
  }
  .spinleftright {
    width: 90px;
  }
  .screensection {
    margin: 2em;
  }
</style>
