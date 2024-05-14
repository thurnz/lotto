<script setup lang="ts">
  import { onMounted, ref, watch, type PropType } from 'vue'
  import { gsap } from 'gsap'
  const drum = ref()
  const max = {x: 240, y: 240}

  const props = defineProps({
    balls: Array as PropType<number[]>,
    isDrawing: Boolean
  })

  const moveBall = (ball: HTMLDivElement) => {
    if(!props.isDrawing) 
      stopBall(ball)
    else
      gsap.to(ball, {duration: Math.random() * 0.25, ease: 'linear', x: Math.random() * max.x, y: Math.random() * max.y, onComplete: moveBall, onCompleteParams: [ball]})
  }

  const stopBall = (ball: HTMLDivElement) => {
    gsap.to(ball, {duration: 0.25, ease: 'linear', x: 50 + Math.random() * max.x, y: 200 +  Math.random() * 50})
  }

  const startBalls = () => {
    const children: HTMLDivElement[] = Object.values(drum.value.children)
    children.forEach((child: HTMLDivElement) => {
      moveBall(child)
    })
  }

  watch(() => props.isDrawing, () => {
    if(props.isDrawing) startBalls()
  })

  onMounted(() => {
    const children: HTMLDivElement[] = Object.values(drum.value.children)
    children.forEach((child: HTMLDivElement) => {
      stopBall(child)
    })
  })
</script>

<template>
  <div ref="drum" class="drum">
    <div v-for="ball in balls" :key="ball" :class="'ball b' + ball"></div>
  </div>
</template>

<style scoped>
  .drum {
    width: 300px;
    height: 300px;
    background-image: radial-gradient(circle, rgba(255,255,255,0) 0%, rgba(255,255,255,0.25) 100%);
    border-radius: 50%;
    position: relative;
    margin: 0 auto;
    overflow: hidden;
  }
  .drum::after {
    content: '';
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    box-shadow: inset 0 0 20px rgba(255, 255, 255, 1);
  }

  .ball {
    background-size: contain;
    background-repeat: no-repeat;
    width: 60px;
    height: 60px;
    position: absolute;
    filter: blur(2px);
  }

  .b0 {
    background-image: url(../assets/images/0.png);
  }

  .b1 {
    background-image: url(../assets/images/1.png);
  }

  .b2 {
    background-image: url(../assets/images/2.png);
  }

  .b3 {
    background-image: url(../assets/images/3.png);
  }

  .b4 {
    background-image: url(../assets/images/4.png);
  }

  .b5 {
    background-image: url(../assets/images/5.png);
  }

  .b6 {
    background-image: url(../assets/images/6.png);
  }

  .b7 {
    background-image: url(../assets/images/7.png);
  }

  .b8 {
    background-image: url(../assets/images/8.png);
  }

  .b9 {
    background-image: url(../assets/images/9.png);
  }
</style>
