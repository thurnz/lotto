<script setup lang="ts">
  import { reactive, ref, onMounted, onUnmounted, computed } from 'vue'
  import LottoDrum from './components/LottoDrum.vue'
  import BetTickets from './components/BetTickets.vue'
  import ResultModal from './components/ResultModal.vue'
  import { Graphics, Container, Sprite, Texture, Assets } from 'pixi.js'
  import { Application, onTick } from 'vue3-pixi'
  import type { ParticleContainerInst } from 'vue3-pixi'
  import Sparks from './assets/images/sparks.png'

  const timer = ref(15)
  const ticketPrice = ref(1)
  const balls = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  const drawnBall = ref(0)
  const isWinner = ref(false)
  const winAmount = 2

  const user = reactive({
    balance: 3,
    tickets: [] as Number[],
  })

  const options: Intl.NumberFormatOptions = {
    style: 'currency', 
    currency: 'EUR'
  };

  const formatCurrency = (n: number) => {
    return new Intl.NumberFormat('en', options).format(n);
  }

  const handleBet = (n: number) => {
    user.balance--
    user.tickets.push(n)
  }

  const nextGame = () => {
    drawnBall.value = 0
    isWinner.value = false
    user.tickets = []
    if(user.balance > 0)
      startTimer()
  }

  const startDraw = () => {
      const n = Math.floor(Math.random() * balls.length)
      drawnBall.value = balls[n]
      isWinner.value = user.tickets.includes(drawnBall.value)
      if(isWinner.value) user.balance += winAmount
      timer.value = 15
      setTimeout(nextGame, 5000)
  }

  const startTimer = () => {
    const interval = setInterval(() => {
      timer.value--
      if(!timer.value){
        clearInterval(interval)
        setTimeout(startDraw, 2000)
      }
    }, 1000)
  }

  const isDrawing = computed(() => {
    return !timer.value
  })

  const width = computed(() => window.innerWidth)
  const height = computed(() => window.innerHeight)

  const color = [0xffffff, 0xff2a5c, 0xe4ff2a, 0xa62aff, 0x3df2da, 0xff2a9c, 0x2aff9f]
  const confettis: any[] = []
  const scale: number[] = []
  const xAdd: number[] = []
  const particleRef = ref<ParticleContainerInst>()

  const createConfetti = () => {
    /*if (!particleRef.value) return

    for(let i = 0; i < 40; i++){
      const colorNum = i % color.length
      scale.push(((Math.floor(Math.random() * 4) + 8) / 10));
      xAdd.push(i % 2 === 0 ? 1 : -1)
      const graphic = new Graphics().fill(color[colorNum]).rect(-5, -5, 10, 10)
      const sprite = new Sprite(Texture.from(Sparks))
      const container = new Container().addChild(sprite)
      container.position.x = Math.floor(Math.random() * width.value) + 10;
      container.position.y = Math.floor(Math.random() * height.value)
      container.scale.x = graphic.scale.y = scale[i]
      container.skew.y = i % 10 * 0.1
      container.rotation = 0.1 + ((i % 5) * 0.1)
      confettis.push(sprite)
    }

    particleRef.value.addChild(...confettis)*/
  }

  const animate = () => {
    const skew = [0.02, 0.04, 0.06, 0.08, 0.1]
    const xPlus = [0.05, 0.1, 0.15, 0.2, 0.25]
    for (let i = 0; i < confettis.length; i++) {
      let y,x
      let colornum = i % color.length
      confettis[i].clear()
      confettis[i].fill(color[colornum]).rect(-5, -5, 10, 10)
      if(confettis[i].y > height.value) {
        y = -10
        x = Math.floor(Math.random() * width.value) + 10
      } else {
        y = confettis[i].y + 0.2 + ((i % 5) * 0.1)
      }
      if (confettis[i].x > width.value - 10) {
        xAdd[i] = -1
      } else if (confettis[i].x < 10) {
        xAdd[i] = 1
      }
      if(xAdd[i] === -1) {
        x = confettis[i].x - xPlus[i % 5]
      } else {
        x = confettis[i].x + xPlus[i % 5]
      }
      confettis[i].scale.x = scale[i]
      confettis[i].scale.y = scale[i]
      confettis[i].position.x = x
      confettis[i].position.y = y
      confettis[i].skew.y += i % 2 === 0 ? skew[i % 5] : skew[i % 5] * -1
      confettis[i].rotation += i % 2 === 0 ? (0.01 + ((i % 5) * 0.01)) : (0.01 + ((i % 5) * -0.01))
    }
  }

  onTick(() => {
    //animate()
  })

  onMounted(() => {
    startTimer()
  })

  //onUnmounted(() => confettis.forEach(confetti => confetti.destroy()))
</script>

<template>
  <Application :width="width" :height="height" :background-color="'#181818'">
    <particle-container ref="particleRef" @render="createConfetti" />
  </Application>
  <div class="container">
    <LottoDrum :is-drawing="isDrawing" :balls="balls"  />
    <h1 v-if="!isDrawing" :class="{red: timer < 6}">PICK YOUR TICKET<br>DRAW WILL BEGIN IN {{ timer }} SECONDS</h1>
    <h1 v-else>DRAWING...<br>GOOD LUCK!</h1>
    <div class="row">
      <h2>BALANCE: {{ formatCurrency(user.balance) }}</h2>
      <h2>TICKETS: {{ user.tickets.length }}</h2>
    </div>
    <BetTickets :disable="user.balance < ticketPrice || isDrawing" @bet="handleBet" />
  </div>
  <ResultModal :show="!!drawnBall" :drawnBall="drawnBall" :winAmount="isWinner ? formatCurrency(winAmount) : ''" />
</template>

<style scoped>
  .container {
    width: calc(100vw - 4rem);
    height: calc(100vh - 4rem);
    position: absolute;
    left: 0;
    top: 0;
  }

  .row {
    margin: 10px auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  h1 {
    text-align: center;
    font-weight: 700;
  }

  h2 {
    background-color: #000;
    padding: 0 10px;
    font-weight: 500;
    color: #00cc00;
  }

  .red {
    color: #f00;
  }
</style>
