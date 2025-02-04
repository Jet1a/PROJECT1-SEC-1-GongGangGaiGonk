<script setup>
import { ref } from "vue";
import wordDictionary from "./data/words_dictionary";

const isHomePage = ref(false);
const isHowToPlayPage = ref(true);
const isGamePage = ref(false);
const isResultPage = ref(false);

const stepCounter = ref(0);
const score = ref(0);
const totalTime = ref(0);
const wordCount = ref(0);
const counter = ref(15);
const wordInput = ref("");
const interval = ref(null);
const usedWord = ref([]);
const inputError = ref("");
const notification = ref("Enter a word to start");
const nextLetter = ref("");

const description = [
  {
    step: 1,
    keyWord: "starting word",
    title: "Enter a starting word",
    img: "/src/images/enterWord.png",
    content:
      "Begin the game by typing a word into the input field and pressing Enter. This will be your starting word.",
  },
  {
    step: 2,
    keyWord: "connecting",
    title: "Connecting word",
    img: "/src/images/connect.png",
    content:
      "Enter words that start with the last letter of the previous word you enter. Each word must be unique and you have 15 seconds to enter each word. If you don’t enter a word in time, the game will end.",
  },
  {
    step: 3,
    keyWord: "repeat word",
    title: "Repeat until beat the game!",
    img: "/src/images/repeat.png",
    content:
      "Do the same step as mention before. The game will continues until you can’t find a new word that starts with the last letter of the previous word or until the timer run out. ",
  },
];

const increment = () => {
  if (stepCounter.value < description.length - 1) {
    stepCounter.value++;
  } else if (stepCounter.value === description.length - 1) {
    isHowToPlayPage.value = false;
    isGamePage.value = true;
  }
  console.log(`increment : ${stepCounter.value}`);
};

const decrement = () => {
  if (stepCounter.value > 0) {
    stepCounter.value--;
  }
  console.log(`decrement : ${stepCounter.value}`);
};

const onBackHome = () => {
  isGamePage.value = false;
  isResultPage.value = false;
  isHowToPlayPage.value = false;
};

const playAgain = () => {
  isGamePage.value = true;
  isResultPage.value = false;
  score.value = 0;
  counter.value = 15;
  wordCount.value = 0;
  clearInterval(interval.value);
  usedWord.value = [];
  inputError.value = "";
  notification.value = "Enter a word to start";
  nextLetter.value = "";
};

const onReset = () => {
  score.value = 0;
  counter.value = 15;
  wordCount.value = 0;
  clearInterval(interval.value);
  usedWord.value = [];
  inputError.value = "";
  notification.value = "Enter a word to start";
  nextLetter.value = "";
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

  counter.value = 2;

  if (interval.value) {
    clearInterval(interval.value);
  }

  interval.value = setInterval(() => {
    if (counter.value > 0) {
      counter.value--;
    } else {
      clearInterval(interval.value);
      interval.value = null;
      isGamePage.value = false;
      isResultPage.value = true;
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
  <main
    class="min-h-screen flex items-center justify-center text-black bg-white"
  >
    <!-- How to Play Page -->
    <section v-show="isHowToPlayPage">
      <div class="bg-white">
        <div class="grid w-full min-h-screen place-items-center py-9">
          <div class="w-full">
            <div class="flex justify-center mb-9">
              <ul class="steps steps-horizontal">
                <li
                  v-for="(step, index) in description"
                  :key="index"
                  class="step"
                  :class="{
                    'mx-auto sm:mx-2 step-primary after:!bg-[#1882FF] before:!bg-[#1882FF] after:!text-white':
                      counter >= index,
                  }"
                >
                  {{ step.keyWord }}
                </li>
              </ul>
            </div>
            <div
              class="hero-content text-center border-2 rounded-xl border-gray-400 p-[40px] w-[50%] h-[30rem] mx-auto bg-white shadow-lg"
            >
              <div class="max-w-md mx-auto">
                <h1 class="text-5xl font-bold text-[#1882FF] my-3">
                  How to Play ?
                </h1>
                <img
                  :src="`${description[stepCounter].img}`"
                  alt="how-to-play"
                  class="w-[70%] mx-auto my-6"
                />
                <p class="text-black font-semibold">
                  {{ description[stepCounter].title }}
                </p>
                <p class="py-2 mb-8 text-gray-500">
                  {{ description[stepCounter].content }}
                </p>
              </div>
            </div>
            <div class="gap-4 grid grid-cols-2 max-w-[30%] mx-auto mt-9">
              <button
                class="btn bg-[#D9D9D9] text-[#595959] border-0"
                @click="decrement"
                :disabled="stepCounter === 0"
              >
                <svg
                  class="w-6 h-6 text-gray-800 dark:text-white"
                  aria-hidden="true"
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  fill="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    fill-rule="evenodd"
                    d="M13.729 5.575c1.304-1.074 3.27-.146 3.27 1.544v9.762c0 1.69-1.966 2.618-3.27 1.544l-5.927-4.881a2 2 0 0 1 0-3.088l5.927-4.88Z"
                    clip-rule="evenodd"
                  />
                </svg>
                Back
              </button>
              <button
                class="btn bg-[#1882FF] border-0 text-white"
                @click="increment"
              >
                Next
                <svg
                  class="w-6 h-6 text-gray-800 dark:text-white"
                  aria-hidden="true"
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  fill="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    fill-rule="evenodd"
                    d="M10.271 5.575C8.967 4.501 7 5.43 7 7.12v9.762c0 1.69 1.967 2.618 3.271 1.544l5.927-4.881a2 2 0 0 0 0-3.088l-5.927-4.88Z"
                    clip-rule="evenodd"
                  />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Game Page -->
    <section v-show="isGamePage">
      <div class="flex flex-col space-y-4">
        <section class="flex items-center justify-between w-[574px]">
          <button
            @click="isGamePage = !isGamePage"
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
                :key="key"
                class="w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
                >{{ value }}</span
              >
            </div>
          </div>
        </section>
      </div>
    </section>

    <!-- Result Page -->
    <section class="w-[700px]" v-show="isResultPage">
      <div class="mb-10">
        <button
          class="text-2xl font-bold border border-gray-200 rounded-md px-8 py-0.5 shadow-lg"
        >
          Back
        </button>
      </div>
      <div class="border border-gray-200 shadow-lg">
        <p class="text-center text-3xl font-bold py-8">Game Over</p>
        <div
          class="flex flex-col items-center sm:flex-row py-3 border-b w-full"
        >
          <div
            class="flex w-1/2 justify-between sm:flex-col sm:items-center sm:w-1/3"
          >
            <p class="text-2xl">Score</p>
            <p class="text-3xl sm:text-5xl">{{ score }}</p>
          </div>
          <div
            class="flex w-1/2 justify-between sm:flex-col items-center sm:w-1/3"
          >
            <p class="text-2xl">Total Time</p>
            <p class="text-3xl sm:text-5xl">{{ totalTime }}</p>
          </div>
          <div
            class="flex w-1/2 justify-between items-start sm:flex-col sm:items-center sm:w-1/3"
          >
            <p class="text-2xl">Word Count</p>
            <p class="text-3xl sm:text-5xl">{{ wordCount }}</p>
          </div>
        </div>
        <div
          class="py-10 flex flex-col items-center gap-4 sm:flex-row sm:justify-center sm:gap-8"
        >
          <button
            class="sm:w-1/4 flex justify-center gap-2 bg-[#1882FF] px-5 py-1.5 text-white fill-white rounded-md font-bold"
            @click="playAgain"
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
          <button
            class="sm:w-1/4 px-5 py-1.5 font-bold border-2 border-black rounded-md"
          >
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
    
  </main>
</template>

<style scoped></style>
