<script setup>
import { ref } from "vue";
import wordDictionary from "./data/words_dictionary";

const score = ref(0);
const counter = ref(15);
const wordCount = ref(0);
const wordInput = ref("");
const interval = ref(null);
const usedWord = ref([]);
const inputError = ref("");
const notification = ref("Enter a word to start");
const nextLetter = ref("");
const gamePageShow = ref(true);

const onReset = () => {
  score.value = 0;
  counter.value = 15;
  wordCount.value = 0;
  clearInterval(interval.value);
  usedWord.value = [];
  inputError.value = "";
  notification.value = "Enter a word to start";
  nextLetter = "";
};

const handleInputChange = (e) => {
  wordInput.value = e.target.value.trim().toUpperCase();

  if (
    wordInput.value === "" ||
    /\d/.test(wordInput.value) ||
    wordInput.value.includes(" ") ||
    !wordDictionary[wordInput.value.toLowerCase()]
  ) {
    inputError.value = `"${wordInput.value}" is not a valid word!`;
    wordInput.value = "";
    return;
  }

  if (wordInput.value.length < 3) {
    inputError.value = `Word must be at least 3 character long!`;
    wordInput.value = "";
    return;
  }

  if (!wordInput.value.charAt(0).includes(nextLetter.value)) {
    inputError.value = `Word must start with letter ${nextLetter.value}`;
    wordInput.value = "";
    return;
  }

  if (usedWord.value.includes(wordInput.value)) {
    inputError.value = `Word have been used.`;
    wordInput.value = "";
    return;
  }

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
      gamePageShow.value = false;
    }
  }, 1000);

  score.value += wordInput.value.length;
  wordCount.value++;

  usedWord.value.push(wordInput.value);
  nextLetter.value = wordInput.value.slice(-1);

  notification.value = `Next word must begin with '${nextLetter.value}'`;
  inputError.value = "";
  wordInput.value = "";
};
</script>

<template>
  <section v-show="gamePageShow">
    <div class="flex flex-col space-y-4">
      <!-- Back & Reset section -->
      <section class="flex items-center justify-between w-[574px]">
        <button
          @click="gamePageShow = !gamePageShow"
          class="px-8 py-2 border rounded-md shadow-md bg-white"
        >
          Back
        </button>
        <span @click="onReset" class="mr-4 cursor-pointer">
          <svg
            class="w-6"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 512 512"
          >
            <path
              d="M463.5 224l8.5 0c13.3 0 24-10.7 24-24l0-128c0-9.7-5.8-18.5-14.8-22.2s-19.3-1.7-26.2 5.2L413.4 96.6c-87.6-86.5-228.7-86.2-315.8 1c-87.5 87.5-87.5 229.3 0 316.8s229.3 87.5 316.8 0c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0c-62.5 62.5-163.8 62.5-226.3 0s-62.5-163.8 0-226.3c62.2-62.2 162.7-62.5 225.3-1L327 183c-6.9 6.9-8.9 17.2-5.2 26.2s12.5 14.8 22.2 14.8l119.5 0z"
            />
          </svg>
        </span>
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
        <div
          :class="{
            'w-full text-center py-2 rounded-sm': true,
            'bg-blue-500': !inputError,
            'bg-red-200': inputError,
          }"
        >
          <span
            :class="{
              'text-2xl font-semibold': true,
              'text-white': !inputError,
              'text-red-500': inputError,
            }"
          >
            {{ inputError || notification }}
          </span>
        </div>

        <div class="border rounded-xl shadow-md">
          <input
            class="w-[574px] h-[65px] text-2xl p-4 bg-white rounded shadow-inner uppercase outline-none"
            @keydown.enter="handleInputChange"
            v-model="wordInput"
            type="text"
          />
        </div>
        <div
          v-show="usedWord.length > 0"
          class="border rounded-md shadow-md w-full max-w-[575px] p-4"
        >
          <span class="text-lg font-light text-neutral-500">Used word</span>
          <div
            class="flex items-center justify-start flex-wrap gap-4 uppercase font-semibold text-[#777777] pt-4"
          >
            <span
              v-for="(value, key) in usedWord"
              class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
              >{{ value }}</span
            >
          </div>
        </div>
      </section>
    </div>
  </section>
</template>

<style scoped></style>
