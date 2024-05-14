<script setup lang="ts">
  import { ref, watch, type Ref } from 'vue'

  const props = defineProps({
    disable: Boolean
  })

  type Ticket = {
    num: number
    picked: boolean
  };

  const tickets: Ref<Ticket[]> = ref([
    { num: 1, picked: false },
    { num: 2, picked: false },
    { num: 3, picked: false },
    { num: 4, picked: false },
    { num: 5, picked: false },
    { num: 6, picked: false },
    { num: 7, picked: false },
    { num: 8, picked: false },
    { num: 9, picked: false },
    { num: 10, picked: false },
  ])

  const emit = defineEmits(['bet'])

  const resetSelected = () => {
    tickets.value.forEach(ticket => ticket.picked = false)
  }

  const handlePicked = (ticket: Ticket) => {
    ticket.picked = true
    emit('bet', ticket.num);
  }

  watch(() => props.disable, () => {
    if(!props.disable) resetSelected()
  })
</script>

<template>
  <div class="tickets">
    <div v-for="ticket in tickets" :key="ticket.num" @click="handlePicked(ticket)"
      :class="{ disable: props.disable && !ticket.picked, picked: ticket.picked }">{{ ticket.num }}</div>
  </div>
</template>

<style scoped>
  .tickets {
    margin: 10px auto;
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  }

  .tickets>div {
    width: calc(15%);
    margin: 2%;
    height: 150px;
    font-size: 4rem;
    font-weight: 700;
    color: #000;
    background-color: #ccc;
    border-radius: 5px;
    text-align: center;
    cursor: pointer;
    line-height: 2;
  }

  .tickets>.picked {
    pointer-events: none;
    background-color: blue;
    color: #fff;
  }

  .tickets>.disable {
    pointer-events: none;
    opacity: 0.25;
  }
</style>
