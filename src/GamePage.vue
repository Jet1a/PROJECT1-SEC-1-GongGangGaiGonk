<script setup>
import { ref, watchEffect } from "vue";

const score = ref(0);
const counter = ref(15);
const wordCount = ref(0);
const wordInput = ref("");
const interval = ref(null);

const handleInputChange = (e) => {
  if (e.target.value.trim() !== "") {
    counter.value = 15;

    if (interval.value) {
      clearInterval(interval.value);
    }

    interval.value = setInterval(() => {
      if (counter.value > 0) {
        counter.value--;
      } else {
        clearInterval(interval.value);
        interval.value = null;
      }
    }, 1000);

    wordInput.value = e.target.value.toUpperCase();
    console.log(wordInput.value);
  }

  wordInput.value = "";
};
</script>

<template>
  <div class="flex flex-col space-y-4">
    <!-- Back & Reset section -->
    <section class="flex items-center justify-between w-[574px]">
      <button class="px-8 py-2 border rounded-md shadow-md bg-white">
        Back
      </button>
      <span class="mr-4">Reset</span>
    </section>

    <!-- Score/Time/WordCount Section -->
    <section class="flex items-center justify-between px-8">
      <div class="flex flex-col items-center justify-center">
        <span>Score</span>
        <span class="text-5xl">{{ score }}</span>
      </div>
      <div class="flex flex-col items-center justify-center">
        <span>Time</span>
        <span class="text-5xl countdown">
          <span :style="{ '--value': counter }"></span>
        </span>
      </div>
      <div class="flex flex-col items-center justify-center">
        <span>Word Count</span>
        <span class="text-5xl">{{ wordCount }}</span>
      </div>
    </section>

    <section class="flex items-center justify-center flex-col space-y-4">
      <div class="w-full bg-blue-500 rounded-sm text-center py-2">
        <span class="text-2xl font-semibold text-white"
          >Enter a word to start</span
        >
      </div>
      <div class="border rounded-xl shadow-md">
        <input
          class="w-[574px] h-[65px] text-2xl p-4 bg-white rounded shadow-inner uppercase outline-none"
          @keyup.enter="handleInputChange"
          v-model="wordInput"
          type="text"
        />
      </div>
      <div class="border rounded-md shadow-md w-full max-w-[575px] p-4">
        <span class="text-lg font-light text-neutral-500">Used word</span>
        <div
          class="flex items-center justify-start flex-wrap gap-4 uppercase font-semibold text-[#777777] pt-4"
        >
          <span class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
            >Hello</span
          >
          <span class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
            >Magnificent</span
          >
          <span class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
            >Banana</span
          >
          <span class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
            >Hello</span
          >
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped></style>
