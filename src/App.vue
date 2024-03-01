<script setup lang="ts">
import { reactive, ref, onMounted, toRef } from 'vue'

let animals: {name: string, picUrl: string}[] = [
  {
    name: 'UNDERBITE',
    picUrl: 'UNDERBITE.png'
  },
  {
    name: 'NEMO',
    picUrl: 'NEMO.png'
  },
  {
    name: 'TIGER',
    picUrl: 'TIGER.png'
  },
  {
    name: 'MACCHIATO',
    picUrl: 'MACCHIATO.png'
  },
  {
    name: 'OTTA',
    picUrl: 'OTTA.png'
  },
  {
    name: 'MOS',
    picUrl: 'MOS.png'
  },
  {
    name: 'AGUA',
    picUrl: 'AGUA.png'
  }
]
let animalsThemeColor: string[] = ['#55562d', '#7a613d', '#8c7047', '#896739', '#796748', '#68503d', '#7a715a']
let selected: boolean[] = [true, false, false, false, false, false, false]
let slideWidth: number = 1740

let fixedWidth = toRef(window.innerWidth)
let movingBar = ref()
let fixedBar = ref()
let selectedIndex = ref(0)
let animalsInfo = reactive(animals)
let selectedArr = ref(selected)
let touchPreX = ref(0)

function move(): void{
  if(selectedIndex.value === 0 || slideWidth < fixedWidth.value) {
    movingBar.value.style.left = '0px'
    return
  }
  let moveDistance = - 135 - 220 * (selectedIndex.value - 1)
  if((slideWidth + moveDistance) >= fixedWidth.value){
    movingBar.value.style.left = moveDistance + 'px'
  }else{
    movingBar.value.style.left = fixedWidth.value - slideWidth + 'px'
  }
}

function selectAnimal(index: number): void{
  if(index === selectedIndex.value) return
  let preIndex = selectedIndex.value
  selectedArr.value[index] = true
  selected[selectedIndex.value] = false
  selectedIndex.value = index
  if(index - preIndex > 0) move()
  if(index - preIndex < 0) move()
}

function clickArrow(direction: number): void{
  if((!direction && selectedArr.value[0]) || (direction && selectedArr.value[6])) return
  selectedArr.value[selectedIndex.value] = false
  direction ? selectedIndex.value++ : selectedIndex.value--
  selectedArr.value[selectedIndex.value] = true
  if(direction) move()
  if(direction === 0) move()
}

function scrollBar(e: MouseEvent){
  let currentX = e.clientX
  document.onmousemove = (moveE: MouseEvent) => {
    moveE.preventDefault()
    scrollAndTouch(moveE.clientX, currentX)
  }
  document.onmouseup = () => {
    document.onmousemove = null
    document.onmouseup = null
  }
}

function touchStartBar(e: TouchEvent){
  touchPreX.value = e.touches[0].pageX
}

function touchMoveBar(e: TouchEvent){
  scrollAndTouch(e.touches[0].pageX, touchPreX.value)
}

function scrollAndTouch(preX: number, currentX: number){
  let distanceX: number = preX - currentX
  let tempSetOffsetLeft = movingBar.value.offsetLeft + distanceX
  if(tempSetOffsetLeft > 0 && tempSetOffsetLeft === 0) movingBar.value.style.left = '0px'
  if(tempSetOffsetLeft < 0 && - tempSetOffsetLeft < slideWidth - fixedWidth.value){
    movingBar.value.style.left = tempSetOffsetLeft + 'px'
  }
}
function getAssetsFile(url: string){
  return new URL(`./assets/${url}`, import.meta.url).href
}

onMounted(() => {
  window.onresize = () => {
    fixedWidth.value = window.innerWidth
    move()
  }
})
</script>

<template>
  <div class="slideBar">
    <div class="left" @click="clickArrow(0)">
      <svg v-if="selectedArr[0]" t="1709175418449" class="nonpointable leftArrow" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4206" width="30" height="30"><path d="M312.888889 995.555556c-17.066667 0-28.444444-5.688889-39.822222-17.066667-22.755556-22.755556-17.066667-56.888889 5.688889-79.644445l364.088888-329.955555c11.377778-11.377778 17.066667-22.755556 17.066667-34.133333 0-11.377778-5.688889-22.755556-17.066667-34.133334L273.066667 187.733333c-22.755556-22.755556-28.444444-56.888889-5.688889-79.644444 22.755556-22.755556 56.888889-28.444444 79.644444-5.688889l364.088889 312.888889c34.133333 28.444444 56.888889 73.955556 56.888889 119.466667s-17.066667 85.333333-51.2 119.466666l-364.088889 329.955556c-11.377778 5.688889-28.444444 11.377778-39.822222 11.377778z" fill="#f3ee9b" p-id="4207"></path></svg>
      <svg v-if="!selectedArr[0]" t="1709175418449" class="pointable leftArrow" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4206" width="30" height="30"><path d="M312.888889 995.555556c-17.066667 0-28.444444-5.688889-39.822222-17.066667-22.755556-22.755556-17.066667-56.888889 5.688889-79.644445l364.088888-329.955555c11.377778-11.377778 17.066667-22.755556 17.066667-34.133333 0-11.377778-5.688889-22.755556-17.066667-34.133334L273.066667 187.733333c-22.755556-22.755556-28.444444-56.888889-5.688889-79.644444 22.755556-22.755556 56.888889-28.444444 79.644444-5.688889l364.088889 312.888889c34.133333 28.444444 56.888889 73.955556 56.888889 119.466667s-17.066667 85.333333-51.2 119.466666l-364.088889 329.955556c-11.377778 5.688889-28.444444 11.377778-39.822222 11.377778z" fill="#ffffff" p-id="4207"></path></svg>
    </div>
    <div class="content" ref="fixedBar">
      <div class="animals" ref="movingBar" @mousedown="scrollBar" @touchstart="touchStartBar" @touchmove="touchMoveBar">
        <div v-for="(animal, index) in animalsInfo" class="avatar" @click="selectAnimal(index)" :style="{'--scale': selectedArr[index] ? '1.1' : '1'}">
          <div class="innerCircle" :style="{'--color': animalsThemeColor[index]}"></div>
          <div v-if="selectedArr[index]" class="selectedCircle"></div>
          <div class="animalCircle">
            <img :src="getAssetsFile(animal.picUrl)" :alt="animal.name" class="animalImg" draggable="false"/>
          </div>
        </div>
      </div>
    </div>
    <div class="right"  @click="clickArrow(1)">
      <svg v-if="selectedArr[6]" t="1709175418449" class="nonpointable rightArrow" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4206" width="30" height="30"><path d="M312.888889 995.555556c-17.066667 0-28.444444-5.688889-39.822222-17.066667-22.755556-22.755556-17.066667-56.888889 5.688889-79.644445l364.088888-329.955555c11.377778-11.377778 17.066667-22.755556 17.066667-34.133333 0-11.377778-5.688889-22.755556-17.066667-34.133334L273.066667 187.733333c-22.755556-22.755556-28.444444-56.888889-5.688889-79.644444 22.755556-22.755556 56.888889-28.444444 79.644444-5.688889l364.088889 312.888889c34.133333 28.444444 56.888889 73.955556 56.888889 119.466667s-17.066667 85.333333-51.2 119.466666l-364.088889 329.955556c-11.377778 5.688889-28.444444 11.377778-39.822222 11.377778z" fill="#f3ee9b" p-id="4207"></path></svg>
      <svg v-if="!selectedArr[6]" t="1709175418449" class="pointable rightArrow" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4206" width="30" height="30"><path d="M312.888889 995.555556c-17.066667 0-28.444444-5.688889-39.822222-17.066667-22.755556-22.755556-17.066667-56.888889 5.688889-79.644445l364.088888-329.955555c11.377778-11.377778 17.066667-22.755556 17.066667-34.133333 0-11.377778-5.688889-22.755556-17.066667-34.133334L273.066667 187.733333c-22.755556-22.755556-28.444444-56.888889-5.688889-79.644444 22.755556-22.755556 56.888889-28.444444 79.644444-5.688889l364.088889 312.888889c34.133333 28.444444 56.888889 73.955556 56.888889 119.466667s-17.066667 85.333333-51.2 119.466666l-364.088889 329.955556c-11.377778 5.688889-28.444444 11.377778-39.822222 11.377778z" fill="#ffffff" p-id="4207"></path></svg>
    </div>
  </div>
</template>

<style scoped>
.slideBar{
  width: 100%;
  height: 300px;
  position: relative;
  display: flex;
  background-color: #FCC954;
}

.left, .right{
  position: absolute;
  width: 6%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
}
.left:hover{
  background: linear-gradient(to right, #fcca54, rgba(255, 255, 255, 0));
}
.right:hover{
  background: linear-gradient(to right, rgba(255, 255, 255, 0), #fcca54);
}
.left{
  left: 0;
  background: linear-gradient(to right, #bbb59f67, rgba(255, 255, 255, 0));
}
.right{
  right: 0;
  background: linear-gradient(to right, rgba(255, 255, 255, 0), #bbb59f67);
}
.leftArrow{
  transform: rotateY(180deg);
}


.content{
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.animals{
  position: absolute;
  --offsetLeft: 0;
  left: var(--offsetLeft);
  top: 50%;
  transform: translate(0, -50%);
  padding-left: 100px;
  padding-right: 100px;
  width: 1540px;
  transition: 0.5s;
}
.avatar{
  --scale: 1;
  display: inline-block;
  position: relative;
  width: 200px;
  height: 200px;
  margin: 0 10px 0 10px;
  border-radius: 50%;
  transition: 0.3s;
  user-select: none;
  transform: scale(var(--scale));
}
.avatar:hover{
  transform: scale(1.1);
}
.innerCircle{
  --inner: 165px;
  --color: #bfa;
  width: var(--inner);
  height: var(--inner);
  border-radius: 50%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  transition: 0.3s;
  background: linear-gradient(var(--color), white);
}
.animalCircle{
  --animal: 165px;
  width: var(--animal);
  height: var(--animal);
  border-radius: 50%;
  border: 10px rgb(250, 248, 248) solid;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  transition: 0.3s;
}
.animalImg{
  --animalIMG: 181px;
  width: var(--animalIMG);
  height: var(--animalIMG);
  position: absolute;
  top: 45%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 0 0 50% 50%;
  transition: 0.3s;
}
.selectedCircle{
  --selected: 175px;
  --border: 15px;
  width: var(--selected);
  height: var(--selected);
  border-radius: 50%;
  border: var(--border) solid orange;
  position: absolute;
  top: 50%;
  left: 50%;
  box-shadow: 3px 3px 3px rgba(99, 85, 60, 0.681);
  transform: translate(-50%, -50%);
  transition: 0.3s;
}
</style>