<script setup>
import GamePage from "./GamePage.vue";
import HowToPlay from './HowToPlay.vue';

</script>

<template>
  <div>
    <HowToPlay />
    <main class="min-h-screen flex items-center justify-center bg-white text-black">
      <GamePage />
    </main>
  </div>

</template>

<style scoped></style>
