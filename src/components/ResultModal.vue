<script setup lang="ts">
  const props = defineProps({
    show: Boolean,
    drawnBall: Number,
    winAmount: String
  })
</script>

<template>
  <div v-if="props.show" class="result">
    <div class="modal">
      <h1>Number drawn: {{ drawnBall }}</h1>
      <h2 v-if="props.winAmount">CONGRATULATIONS! YOU WON {{ props.winAmount }}</h2>
      <h2 v-else>SORRY! YOU LOSE</h2>
    </div>
  </div>
</template>

<style scoped>
  h1 {
    font-weight: 700;
    font-size: 3rem;
    text-align: center;
  }

  h2 {
    font-weight: 700;
    font-size: 2rem;
    text-align: center;
  }

  .result {
    position: fixed;
    left: 0;
    top: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.5);
    animation: fade-in 0.5s ease-out forwards;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal {
    position: relative;
    color: #000;
    width: 50vw;
    padding: 50px;
    border-radius: 10px;
    background-color: #ccc;
    animation: pop-out 0.5s ease-out forwards;
  }

  @keyframes fade-in {
    0% {opacity: 0;}
    100% {opacity: 1;}
  }

  @keyframes pop-out {
    0% {transform: scale(0);}
    60% {transform: scale(1.25);}
    80% {transform: scale(0.8);}
    100% {transform: scale(1);}
  }
</style>
