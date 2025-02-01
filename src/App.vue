<script setup>
import { ref } from "vue";
const score = ref(0);
const totalTime = ref(0);
const wordCount = ref(0);
const counter = ref(0);
const usedWord = [
  "Dear",
  "Beer",
  "fruit",
  "dog",
  "bark",
  "though",
  "three",
  "elephant",
];
const playAgain = () => {
  counter.value = 15;
  console.log(counter.value);
};
</script>

<template>
  <main
    class="min-h-screen flex items-center justify-center bg-green-400 text-black font-mono"
  >
    <h1 class="text-2xl">Project1 GongGangGaiGonk</h1>
  </main>
</template>

<style scoped></style>
