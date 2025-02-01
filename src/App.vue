<script setup>
import { ref } from "vue";
import GamePage from "./GamePage.vue";
import HowToPlay from "./HowToPlay.vue";
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
    class="min-h-screen flex items-center justify-center text-black font-mono"
  >
    <section class="w-[700px]" v-show="counter == 0">
      <div class="mb-10">
        <button
          class="text-2xl font-bold border border-gray-200 rounded-md px-8 py-0.5 shadow-lg"
        >
          Back
        </button>
      </div>
      <div class="border border-gray-200 shadow-lg">
        <p class="text-center text-3xl font-bold py-8">Game Over</p>
        <div class="flex flex-col items-center sm:flex-row py-3 border-b w-full">
          <div class="flex w-1/2 justify-between sm:flex-col sm:items-center sm:w-1/3">
            <p class="text-2xl ">Score</p>
            <p class="text-3xl sm:text-5xl">{{ score }}</p>
          </div>
          <div class="flex w-1/2 justify-between sm:flex-col items-center sm:w-1/3">
            <p class="text-2xl">Total Time</p>
            <p class="text-3xl sm:text-5xl">{{ totalTime }}</p>
          </div>
          <div class="flex w-1/2 justify-between items-start sm:flex-col  sm:items-center sm:w-1/3">
            <p class="text-2xl">Word Count</p>
            <p class="text-3xl sm:text-5xl">{{ wordCount }}</p>
          </div>
        </div>
        <div class="py-10 flex flex-col items-center gap-4 sm:flex-row sm:justify-center sm:gap-8">
          <button
            class="sm:w-1/4 flex justify-center gap-2 bg-[#1882FF] px-5 py-1.5 text-white fill-white rounded-md font-bold"
            @click="playAgain()"
          >
            <svg
              class="w-6"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 512"
            >
              <path
                d="M463.5 224l8.5 0c13.3 0 24-10.7 24-24l0-128c0-9.7-5.8-18.5-14.8-22.2s-19.3-1.7-26.2 5.2L413.4 96.6c-87.6-86.5-228.7-86.2-315.8 1c-87.5 87.5-87.5 229.3 0 316.8s229.3 87.5 316.8 0c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0c-62.5 62.5-163.8 62.5-226.3 0s-62.5-163.8 0-226.3c62.2-62.2 162.7-62.5 225.3-1L327 183c-6.9 6.9-8.9 17.2-5.2 26.2s12.5 14.8 22.2 14.8l119.5 0z"
              />
            </svg>
            Play Again
          </button>
          <button class="sm:w-1/4 px-5 py-1.5 font-bold border-2 border-black rounded-md">
            See your stats
          </button>
        </div>
      </div>
      <div class="border-2 mt-8 py-4 px-8 shadow-md">
        <p class="text-xl text-gray-500">Used word</p>
        <div class="flex flex-wrap">
          <p
            v-for="word in usedWord"
            class="bg-gray-300 w-fit px-4 py-1.5 rounded-lg m-2 font-bold uppercase text-gray-500 text-xl"
          >
            {{ word }}
          </p>
        </div>
      </div>
    </section>
    <GamePage/>
    <HowToPlay/>
  </main>

</template>

<style scoped></style>
