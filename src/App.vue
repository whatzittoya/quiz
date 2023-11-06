<template>
  <component :is="currentView" />
</template>

<script setup>
import { ref, computed } from 'vue'
import Quiz from './components/Quiz.vue'
import Welcome from './components/Welcome.vue'
import Countdown from './components/Countdown.vue'

const routes = {
  '/': Welcome,
  '/Countdown': Countdown,
  '/Quiz': Quiz,
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})


</script>

<style></style>
