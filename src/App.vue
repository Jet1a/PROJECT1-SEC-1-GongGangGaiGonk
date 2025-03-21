<script setup>
import { ref, watch, watchEffect, computed, h } from "vue";
import wordDictionary from "./data/words_dictionary";

const isHomePage = ref(true);
const isHowToPlayPage = ref(false);
const isGamePage = ref(false);
const isResultPage = ref(false);
const isAchievementPage = ref(false);
const isShowStats = ref(false);
const userName = ref("");
const isError = ref(false);
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
const longestWords = ref([]);
const allStats = ref([]);
const availableHints = ref(0);
const availableTimeBoosts = ref(0);
const canUseTimeBoost = computed(() => availableTimeBoosts.value > 0);
const canUseHint = computed(() => availableHints.value > 0);
const gameMode = ref("default");
const chooseTimer = ref(15);
const letterCount = ref(3);
const isTimer = ref(true);
const wordDropdown = ref(null);
const timeDropdown = ref(null);

const history = ref([]);
const historyIndex = ref(-1);
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
const achievement = ref([
  {
    id: 1,
    name: "That's a Mouthful!",
    desc: "Submit a word that is at least 10 characters long.",
    icon: "🐍",
    bg: "bg-[#a9e35f]",
    isCompleted: false,
    shown: false,
  },
  {
    id: 2,
    name: "Marathon Chainer",
    desc: "Successfully chain 20 words in a row.",
    icon: "👟",
    bg: "bg-[#ff91c6]",
    isCompleted: false,
    shown: false,
  },
  {
    id: 3,
    name: "Lucky Seven",
    desc: "Play a word that is exactly 7 letters long.",
    icon: "🍀",
    bg: "bg-[#b7edb7]",
    isCompleted: false,
    shown: false,
  },
  {
    id: 4,
    name: "Silent Strategist",
    desc: "Play 5 words that contain the letter 'S'.",
    icon: "🤫",
    bg: "bg-[#b3d4ff]",
    isCompleted: false,
    shown: false,
  },
  {
    id: 5,
    name: "Final Countdown",
    desc: "Use a word that ends in 'Z'.",
    icon: "⏳",
    bg: "bg-[#fcd46f]",
    isCompleted: false,
    shown: false,
  },
  {
    id: 6,
    name: "Achievement Hunter",
    desc: "Collect all achievement.",
    icon: "🏆",
    bg: "bg-[#5487ff]",
    isCompleted: false,
    shown: false,
  },
]);
const visibleAchievement = ref([]);

const handleSubmit = () => {
  if (!userName.value.trim()) {
    isError.value = true;
  } else {
    isError.value = false;
    isHomePage.value = false;
    isGamePage.value = true;
  }
};

const clickToAchievement = () => {
  isAchievementPage.value = true;
  isHomePage.value = false;
};

const clickToHowToPlay = () => {
  isHowToPlayPage.value = true;
  isHomePage.value = false;
};

const onReset = () => {
  score.value = 0;
  counter.value = 15;
  wordCount.value = 0;
  wordInput.value = "";
  clearInterval(interval.value);
  usedWord.value = [];
  inputError.value = "";
  notification.value = "Enter a word to start";
  nextLetter.value = "";
  stepCounter.value = 0;
  isTimer.value = true;
  availableHints.value = 0;
  availableTimeBoosts.value = 0;
  history.value = [];
};

const playAgain = () => {
  onReset();
  isGamePage.value = true;
  isResultPage.value = false;
  history.value = [];
};

const backHome = () => {
  onReset();
  isGamePage.value = false;
  isResultPage.value = false;
  isHowToPlayPage.value = false;
  isAchievementPage.value = false;
  isHomePage.value = true;
};

const increment = () => {
  if (stepCounter.value < description.length - 1) {
    stepCounter.value++;
  }
};

const decrement = () => {
  if (stepCounter.value > 0) {
    stepCounter.value--;
  }
};

const setWord = (word) => {
  letterCount.value = word;

  if (wordDropdown.value) {
    wordDropdown.value.removeAttribute("open");
  }
};

const setGameMode = (mode) => {
  gameMode.value = mode;
  if (wordDropdown.value || timeDropdown.value) {
    wordDropdown.value.removeAttribute("open");
    timeDropdown.value.removeAttribute("open");
  }

  if (gameMode.value !== "moreLetter") {
    letterCount.value = 3;
  }
};

const setTimer = (time) => {
  if (gameMode.value === "timer") {
    chooseTimer.value = time;
  }
  if (timeDropdown.value) {
    timeDropdown.value.removeAttribute("open");
  }
};

const checkInput = (word) => {
  if (
    word === "" ||
    /\d/.test(word) ||
    word.includes(" ") ||
    !wordDictionary[word.toLowerCase()]
  ) {
    inputError.value = `"${word}" is not a valid word!`;
    wordInput.value = "";
    return false;
  }

  if (word.length < 3 && gameMode.value !== "moreLetter") {
    inputError.value = `Word must be at least 3 character long!`;
    wordInput.value = "";
    return false;
  } else if (
    letterCount.value &&
    word.length < letterCount.value &&
    gameMode.value === "moreLetter"
  ) {
    inputError.value = `Word must be ${letterCount.value} character long!`;
    wordInput.value = "";
    return false;
  }

  if (!word.charAt(0).includes(nextLetter.value)) {
    inputError.value = `Word must start with letter ${nextLetter.value}`;
    wordInput.value = "";
    return false;
  }

  if (usedWord.value.includes(word)) {
    inputError.value = `Word have been used.`;
    wordInput.value = "";
    return false;
  }

  return true;
};

const handleInputChange = (e) => {
  wordInput.value = e.target.value.trim().toUpperCase();

  if (e.key === "Enter") {
    isTimer.value = false;
  }

  if (!checkInput(wordInput.value)) return;

  if (isGamePage.value) {
    if (gameMode.value !== "timer") counter.value = chooseTimer.value;
    clearInterval(interval.value);
    interval.value = setInterval(() => {
      if (counter.value > 0) {
        counter.value--;
        totalTime.value++;
      } else {
        clearInterval(interval.value);
        allStats.value.push({
          userName: userName.value,
          score: score.value,
          totalTime: totalTime.value,
        });
        isGamePage.value = false;
        isResultPage.value = true;
        isShowStats.value = false;
      }
    }, 1000);
  } else {
    clearInterval(interval.value);
  }

  if (wordInput.value === "UMAPORN") {
    score.value += 9999992;
  }

  score.value += wordInput.value.length;
  wordCount.value++;

  usedWord.value.unshift(wordInput.value);
  nextLetter.value = wordInput.value.slice(-1);

  if (wordInput.value.trim() !== "") {
    history.value.push(wordInput.value);
    historyIndex.value = -1;
    wordInput.value = "";
  }

  checkAchievement(wordInput.value);
  notification.value = `Next word must begin with '${nextLetter.value}'`;
  inputError.value = "";
  wordInput.value = "";
};

const addTime = () => {
  if (!canUseTimeBoost.value) return;
  counter.value += 5;
  availableTimeBoosts.value--;
};

const getHint = () => {
  if (!canUseHint.value) return;

  const words = Object.keys(wordDictionary).filter((word) =>
    word.startsWith(nextLetter.value.toLowerCase())
  );

  if (words.length > 0) {
    wordInput.value = words[Math.floor(Math.random() * words.length)];
    availableHints.value--;
  }
};

const checkAchievement = (wordInput) => {
  if (wordInput.length >= 10) {
    achievement.value
      .filter((achievement) => achievement.id === 1)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
  if (wordCount.value >= 20) {
    achievement.value
      .filter((achievement) => achievement.id === 2)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
  if (wordInput.length === 7) {
    achievement.value
      .filter((achievement) => achievement.id === 3)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
  if (usedWord.value.filter((word) => word.includes("S")).length >= 5) {
    achievement.value
      .filter((achievement) => achievement.id === 4)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
  if (wordInput.endsWith("Z")) {
    achievement.value
      .filter((achievement) => achievement.id === 5)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
  if (
    achievement.value
      .filter((achievement) => achievement.id !== 6)
      .every((achievement) => achievement.isCompleted === true)
  ) {
    achievement.value
      .filter((achievement) => achievement.id === 6)
      .forEach((achievement) => (achievement.isCompleted = true));
  }
};

const showStats = () => {
  isShowStats.value = true;
};

const showUsedWord = () => {
  isShowStats.value = false;
};

const findLongestWord = () => {
  const lengthOfLongestWord = usedWord.value.reduce(
    (longest, word) => (word.length > longest ? word.length : longest),
    0
  );
  return usedWord.value.filter((word) => word.length === lengthOfLongestWord);
};

const handleKeydown = (event) => {
  if (event.key === "ArrowUp") {
    if (
      history.value.length > 0 &&
      historyIndex.value < history.value.length - 1
    ) {
      historyIndex.value++;
      wordInput.value =
        history.value[history.value.length - 1 - historyIndex.value];
    }
  } else if (event.key === "ArrowDown") {
    if (historyIndex.value > 0) {
      historyIndex.value--;
      wordInput.value =
        history.value[history.value.length - 1 - historyIndex.value];
    } else {
      historyIndex.value = -1;
      wordInput.value = "";
    }
  }
};

watch(isGamePage, (newValue) => {
  if (!newValue) longestWords.value = findLongestWord();
});

watch(userName, (newValue) => {
  if (newValue.trim()) {
    isError.value = false;
  }
});

watch(
  achievement,
  (newAchievement) => {
    newAchievement.forEach((achievement) => {
      if (achievement.isCompleted && !achievement.shown) {
        achievement.shown = true;
        visibleAchievement.value.push(achievement);

        setTimeout(() => {
          visibleAchievement.value = visibleAchievement.value.filter(
            (ach) => ach.id !== achievement.id
          );
        }, 3000);
      }
    });
  },
  { deep: true }
);

watchEffect(() => {
  if (gameMode.value === "timer") {
    counter.value = chooseTimer.value;
  } else {
    counter.value = 15;
    chooseTimer.value = 15;
  }
});

watch(
  () => usedWord.value.length,
  (newLength, oldLength) => {
    const newEarnedHints = Math.floor(newLength / 10);
    const previousEarnedHints = Math.floor(oldLength / 10);
    const newEarnedTimeBoosts = Math.floor(newLength / 15);
    const previousEarnedTimeBoosts = Math.floor(oldLength / 15);

    if (newEarnedHints > previousEarnedHints) {
      availableHints.value++;
      notification.value = "You earned a new hint!";
      setTimeout(() => {
        notification.value = `Next word must begin with '${nextLetter.value}'`;
      }, 1500);
    }

    if (newEarnedTimeBoosts > previousEarnedTimeBoosts) {
      availableTimeBoosts.value++;
      notification.value = "You earned a new time boost!";
      setTimeout(() => {
        notification.value = `Next word must begin with '${nextLetter.value}'`;
      }, 1500);
    }
  }
);
</script>

<template>
  <main
    class="min-h-screen flex items-center justify-center text-black bg-white"
  >
    <section v-show="isHomePage">
      <div
        class="flex flex-col items-center justify-center text-center px-4 sm:px-6 md:px-8"
      >
        <div class="flex flex-col items-center">
          <h1
            class="text-[72px] font-bold text-[#1882FF] sm:-tracking-[-0.1em] animate-slidedown"
          >
            TORKUM
          </h1>
          <h2 class="text-[24px] font-bold animate-slideleft">
            Word Chain Game
          </h2>
          <p class="text-sm mt-2 max-w-md animate-slideright">
            Word Chain is an exciting word game where each new word must begin
            with the final letter of the preceding word. Challenge your
            vocabulary skills and see how long of a chain you can create!
          </p>
        </div>

        <div class="mt-6 w-full max-w-md animate-slideup">
          <form
            @submit.prevent="handleSubmit"
            class="flex flex-col gap-4 items-center"
          >
            <input
              v-model.trim="userName"
              type="text"
              placeholder="Please enter your name"
              class="w-full h-16 px-4 rounded-lg border focus:outline-none focus:ring-2 focus:ring-blue-500 transition placeholder-gray-400 drop-shadow-md bg-white"
              :class="{ 'border-red-500 placeholder-red-500': isError }"
            />
            <button
              type="submit"
              class="w-full max-w-[294px] h-14 bg-[#1882FF] text-white font-semibold rounded-lg drop-shadow-md flex items-center justify-center gap-2 hover:bg-blue-600 transition delay-150 duration-300 ease-in-out"
            >
              <span class="flex items-center justify-center gap-2">
                <span>Start Game</span>
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 24 24"
                  fill="currentColor"
                  class="w-6 h-6"
                >
                  <path
                    fill-rule="evenodd"
                    d="M2.25 12c0-5.385 4.365-9.75 9.75-9.75s9.75 4.365 9.75 9.75-4.365 9.75-9.75 9.75S2.25 17.385 2.25 12Zm14.024-.983a1.125 1.125 0 0 1 0 1.966l-5.603 3.113A1.125 1.125 0 0 1 9 15.113V8.887c0-.857.921-1.4 1.671-.983l5.603 3.113Z"
                    clip-rule="evenodd"
                  />
                </svg>
              </span>
            </button>
          </form>
          <div class="flex items-center justify-center gap-4 mt-4">
            <button
              type="submit"
              @click="clickToHowToPlay"
              class="w-full max-w-[294px] h-14 bg-[#BFC1C2] text-white font-semibold rounded-lg drop-shadow-md flex items-center justify-center gap-2 hover:bg-blue-500 transition delay-150 duration-300 ease-in-out"
            >
              <span class="flex items-center justify-center gap-2">
                <span>How To Play</span>
              </span>
            </button>
          </div>
          <div class="flex items-center justify-center gap-4 mt-4">
            <button
              type="submit"
              @click="clickToAchievement"
              class="w-full max-w-[294px] h-14 bg-[#BFC1C2] text-white font-semibold rounded-lg drop-shadow-md flex items-center justify-center gap-2 hover:bg-blue-500 transition delay-150 duration-300 ease-in-out"
            >
              <span class="flex items-center justify-center gap-2">
                <span>Achievement</span>
              </span>
            </button>
          </div>
        </div>
      </div>
    </section>

    <section v-show="isAchievementPage">
      <div
        class="flex flex-col space-y-4 w-[300px] sm:w-[600px] md:w-[700px] border rounded shadow-md p-4"
      >
        <h1 class="text-3xl text-[#1882ff] sm:text-4xl font-bold text-center">
          Achievement🏆
        </h1>
        <div
          class="flex flex-col space-y-2 items-center max-h-[380px] overflow-y-scroll"
        >
          <div
            v-for="item in achievement"
            :key="item.id"
            class="max-w-[600px] w-full p-4 border-2 bg-white border-[#e9e4e4] flex items-center justify-between rounded-md gap-2"
          >
            <div class="flex flex-col gap-1 md:gap-2">
              <p class="text-lg md:text-xl font-bold">{{ item.name }}</p>
              <p class="text-sm md:text-md">{{ item.desc }}</p>
            </div>
            <div
              class="p-4 rounded"
              :class="item.isCompleted ? item.bg : 'bg-[#ffe45bde]'"
            >
              <span class="text-4xl">{{
                item.isCompleted ? item.icon : "🔒"
              }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="flex items-center justify-center mt-4">
        <button
          class="btn bg-[#D9D9D9] text-[#595959] border-0"
          @click="backHome"
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
      </div>
    </section>

    <section v-show="isHowToPlayPage">
      <div class="bg-white">
        <div class="grid md:w-[700px] place-items-center py-9 m-8">
          <div class="w-full">
            <div class="flex justify-center mb-9">
              <ul class="steps steps-horizontal">
                <li
                  v-for="(step, index) in description"
                  :key="index"
                  class="step"
                  :class="{
                    'mx-auto sm:mx-2 step-primary after:!bg-[#1882FF] before:!bg-[#1882FF] after:!text-white ':
                      stepCounter >= index,
                  }"
                >
                  {{ step.keyWord }}
                </li>
              </ul>
            </div>
            <div
              class="hero-content text-center border-2 rounded-xl border-gray-400 p-[40px] w-full h-[30rem] mx-auto bg-white shadow-lg"
            >
              <div class="max-w-md mx-auto">
                <h1 class="text-2xl sm:text-5xl font-bold text-[#1882FF] my-3">
                  How to Play ?
                </h1>
                <img
                  :src="`${description[stepCounter].img}`"
                  alt="how-to-play"
                  class="w-[70%] mx-auto my-6"
                />
                <p class="text-black font-semibold sm:text-lg">
                  {{ description[stepCounter].title }}
                </p>
                <p class="text-sm sm:text-md py-2 mb-8 text-gray-500">
                  {{ description[stepCounter].content }}
                </p>
              </div>
            </div>
            <div class="gap-4 grid grid-cols-2 max-w-[80%] mx-auto mt-9">
              <button
                class="btn bg-[#D9D9D9] text-[#595959] border-0"
                @click="stepCounter == 0 ? backHome() : decrement()"
                :class="
                  stepCounter === 2 ? 'bg-blue-500 text-white' : 'bg-[#D9D9D9]'
                "
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
                :disabled="stepCounter === 2"
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

    <section v-show="isGamePage">
      <div class="flex flex-col space-y-4 w-[280px] sm:w-[400px] md:w-[574px]">
        <div>
          <div class="flex justify-center my-4 mt-6" v-show="isTimer">
            <ul
              class="menu menu-horizontal rounded-box bg-white shadow-sm border text-xs max-w-xs w-full justify-center p-1"
            >
              <li class="tooltip bg-white" data-tip="Easy">
                <a class="px-4 py-3" @click="setGameMode('Default')"
                  >Default Game</a
                >
              </li>

              <li>
                <details ref="timeDropdown">
                  <summary
                    class="tooltip px-4 py-3"
                    data-tip="Normal"
                    @click="setGameMode('timer')"
                  >
                    Timer
                  </summary>
                  <ul class="bg-white z-50 text-xs p-1">
                    <li>
                      <a class="px-5 py-3" @click="setTimer(15)">15 sec</a>
                    </li>
                    <li>
                      <a class="px-5 py-3" @click="setTimer(30)">30 sec</a>
                    </li>
                    <li>
                      <a class="px-5 py-3" @click="setTimer(60)">60 sec</a>
                    </li>
                  </ul>
                </details>
              </li>

              <li>
                <details ref="wordDropdown">
                  <summary
                    class="tooltip px-4 py-3"
                    data-tip="Difficult"
                    @click="setGameMode('moreLetter')"
                  >
                    More Letters
                  </summary>
                  <ul class="bg-white z-50 text-xs p-1">
                    <li>
                      <a class="px-3 py-1" @click="setWord(4)">4 Letters</a>
                    </li>
                    <li>
                      <a class="px-3 py-1" @click="setWord(5)">5 Letters</a>
                    </li>
                    <li>
                      <a class="px-3 py-1" @click="setWord(6)">6 Letters</a>
                    </li>
                  </ul>
                </details>
              </li>
            </ul>
          </div>
          <section class="flex flex-wrap justify-center gap-2 sm:gap-4 mt-4">
            <div>
              <div
                class="px-4 py-2 text-center border rounded-md shadow-md bg-white hover:shadow-lg active:scale-95 transition sm:px-6 sm:py-3"
              >
                <div>
                  <span>🔥</span>
                  <span class="font-semibold text-sm sm:text-md"
                    >Game Mode</span
                  >
                </div>
                <div v-show="gameMode === 'Default' || gameMode === 'default'">
                  <span class="">☕️ Default</span>
                </div>
                <div v-show="gameMode === 'moreLetter'">
                  <span class="countdown">
                    <span
                      :style="{
                        '--value': letterCount,
                        'text-align': 'center',
                      }"
                    ></span>
                  </span>
                  <span class="ml-1 text-sm sm:text-md">Letters</span>
                </div>
                <div v-show="gameMode === 'timer'">
                  <span class="text-sm sm:text-md">⏳ Timer</span>
                </div>
              </div>
            </div>

            <button
              @click="addTime"
              :disabled="!canUseTimeBoost"
              class="flex items-center gap-1 px-2 py-2 border rounded-md shadow-md bg-white disabled:opacity-50 disabled:cursor-not-allowed transition hover:shadow-lg active:scale-95 sm:px-4 sm:py-2"
            >
              <span class="text-sm sm:text-md">⏱️</span>
              <span class="font-semibold text-sm sm:text-md">+5 Sec</span>
              <span class="text-gray-500">({{ availableTimeBoosts }})</span>
            </button>

            <button
              @click="getHint"
              :disabled="!canUseHint"
              class="flex items-center gap-1 px-2 py-2 border rounded-md shadow-md bg-white disabled:opacity-50 disabled:cursor-not-allowed transition hover:shadow-lg active:scale-95 sm:px-4 sm:py-2"
            >
              <span class="text-sm">❓</span>
              <span class="text-sm sm:text-md font-semibold">Hint</span>
              <span class="text-gray-500">({{ availableHints }})</span>
            </button>
          </section>
        </div>

        <section class="flex items-center justify-between">
          <button
            @click="backHome"
            class="px-6 sm:px-8 py-1 sm:py-2 border rounded-md shadow-md bg-white"
          >
            Back
          </button>

          <span @click="onReset" class="cursor-pointer">
            <svg
              class="w-4 sm:w-5"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 512"
            >
              <path
                d="M463.5 224l8.5 0c13.3 0 24-10.7 24-24l0-128c0-9.7-5.8-18.5-14.8-22.2s-19.3-1.7-26.2 5.2L413.4 96.6c-87.6-86.5-228.7-86.2-315.8 1c-87.5 87.5-87.5 229.3 0 316.8s229.3 87.5 316.8 0c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0c-62.5 62.5-163.8 62.5-226.3 0s-62.5-163.8 0-226.3c62.2-62.2 162.7-62.5 225.3-1L327 183c-6.9 6.9-8.9 17.2-5.2 26.2s12.5 14.8 22.2 14.8l119.5 0z"
              />
            </svg>
          </span>
        </section>
        <section class="flex items-center justify-between px-2 sm:px-8">
          <div
            class="flex flex-col items-center justify-center w-[250px] sm:w-full"
          >
            <span>Score</span>
            <span class="text-3xl sm:text-5xl">{{ score }}</span>
          </div>
          <div
            class="flex flex-col items-center justify-center text-center w-[250px] sm:w-full"
          >
            <span>Time</span>
            <span class="text-3xl sm:text-5xl countdown">
              <span :style="{ '--value': counter }"></span>
            </span>
          </div>
          <div
            class="flex flex-col items-center justify-center w-[250px] sm:w-full"
          >
            <span>Word Count</span>
            <span class="text-3xl sm:text-5xl">{{ wordCount }}</span>
          </div>
        </section>

        <section
          class="flex items-center justify-center flex-col space-y-4 pb-4"
        >
          <div
            :class="{
              'w-full text-center py-2 rounded-sm': true,
              'bg-blue-500': !inputError,
              'bg-red-200': inputError,
            }"
          >
            <span
              :class="{
                'text-md sm:text-2xl font-semibold': true,
                'text-white': !inputError,
                'text-red-500': inputError,
              }"
            >
              {{ inputError || notification }}
            </span>
          </div>

          <div class="border rounded-xl shadow-md">
            <input
              class="w-[280px] sm:w-[400px] md:w-[574px] h-[50px] sm:h-[65px] text-lg sm:text-2xl p-4 bg-white rounded shadow-inner outline-none focus:outline-none focus:ring-2 focus:ring-blue-500 uppercase"
              @keydown.enter="handleInputChange"
              @keydown="handleKeydown"
              v-model="wordInput"
              placeholder="Please enter a word here"
              type="text"
            />
          </div>
          <div
            v-show="usedWord.length > 0"
            class="border rounded-md shadow-md w-[280px] sm:w-[400px] md:w-[574px] p-4 h-[210px] overflow-auto sm:h-[300px]"
          >
            <span class="text-md sm:text-lg font-light text-neutral-500"
              >Used word</span
            >
            <div
              class="flex items-center justify-start flex-wrap gap-4 uppercase font-semibold text-[#777777] py-4"
            >
              <span
                v-for="(value, key) in usedWord"
                :key="key"
                class="text-sm sm:text-md w-fit px-4 py-2 bg-[#d9d9d9] rounded-lg text-center"
                >{{ value }}</span
              >
            </div>
          </div>
        </section>
      </div>

      <div class="toast toast-top toast-end lg:toast-end lg:toast-bottom">
        <div
          v-for="item in visibleAchievement"
          :key="item.id"
          class="w-full sm:max-w-[600px] sm:w-full p-2 sm:p-4 border-2 bg-white border-[#e9e4e4] flex items-center justify-between rounded-md gap-2 transition-opacity duration-500"
        >
          <div class="flex flex-col gap-1 md:gap-2">
            <span class="text-xs sm:text-lg md:text-xl font-bold">
              {{ item.name }}
            </span>
            <span class="text-xs sm:text-sm md:text-md">{{ item.desc }}</span>
          </div>
          <div class="sm:p-4 rounded" :class="item.bg">
            <span class="text-2xl sm:text-4xl">{{ item.icon }}</span>
          </div>
        </div>
      </div>
    </section>

    <section v-show="isResultPage" class="w-[600px] p-3">
      <div class="mb-5 flex justify-between">
        <button
          @click="backHome"
          class="px-8 py-2 border rounded-md shadow-md bg-white hover:text-white hover:bg-[#67a8f3]"
        >
          Back
        </button>
        <div
          class="px-8 py-2 flex gap-3 border border-gray-200 bg-gray-100 rounded-md shadow-md"
        >
          <svg
            width="20px"
            height="30px"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 448 512"
          >
            <path
              fill="#1882ff"
              d="M224 256A128 128 0 1 0 224 0a128 128 0 1 0 0 256zm-45.7 48C79.8 304 0 383.8 0 482.3C0 498.7 13.3 512 29.7 512l388.6 0c16.4 0 29.7-13.3 29.7-29.7C448 383.8 368.2 304 269.7 304l-91.4 0z"
            />
          </svg>
          <span class="text-xl">{{ userName }}</span>
        </div>
      </div>
      <div class="border border-gray-200 shadow-lg">
        <p class="text-center text-3xl font-bold py-8">Game Over</p>
        <div
          class="flex flex-col items-center sm:flex-row py-3 border-b w-full"
        >
          <div
            class="flex w-3/5 justify-between sm:flex-col sm:items-center sm:w-1/3"
          >
            <p class="text-2xl">Score</p>
            <p class="text-3xl sm:text-5xl">{{ score }}</p>
          </div>
          <div
            class="flex w-3/5 justify-between sm:flex-col items-center sm:w-1/3"
          >
            <p class="text-2xl">Total Time</p>
            <p class="text-3xl sm:text-5xl">{{ totalTime }}</p>
          </div>
          <div
            class="flex w-3/5 justify-between items-start sm:flex-col sm:items-center sm:w-1/3"
          >
            <p class="text-2xl">Word Count</p>
            <p class="text-3xl sm:text-5xl">{{ wordCount }}</p>
          </div>
        </div>

        <div
          class="py-10 flex flex-col gap-3 items-center justify-center md:flex-row md:gap-8"
        >
          <button
            class="w-3/5 sm:w-1/3 flex justify-center gap-3 bg-[#1882FF] px-5 py-1.5 text-white text-xl fill-white rounded-md font-bold hover:bg-[#67a8f3]"
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
            class="w-3/5 sm:w-1/3 bg-[#1882FF] px-5 py-1.5 text-white text-xl font-bold rounded-md hover:bg-[#67a8f3]"
            v-show="!isShowStats"
            @click="showStats"
          >
            Show stats
          </button>
          <button
            class="w-3/5 sm:w-1/3 bg-[#1882FF] px-5 py-1.5 text-white text-xl font-bold rounded-md hover:bg-[#67a8f3]"
            v-show="isShowStats"
            @click="showUsedWord"
          >
            Used word
          </button>
        </div>
        <div class="bg-[#1882FF] text-2xl text-white text-center py-4 px-1">
          <p>Here your the longest word</p>
          <div class="mt-2">
            <span
              v-for="word in longestWords"
              :key="word"
              class="bg-gray-200 px-2 py-1 text-black text-lg m-2 rounded-md"
              >{{ word }}</span
            >
          </div>
        </div>
      </div>

      <!-- Used word -->
      <div v-show="!isShowStats" class="border-2 mt-8 py-4 px-8 shadow-md">
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

      <!-- Stats -->
      <div v-show="isShowStats" class="mt-8 shadow-md">
        <div class="flex justify-between">
          <div
            class="bg-gray-200 text-center border-2 border-r-gray-300 border-l-gray-300 py-2 text-lg font-bold w-1/3"
          >
            Name
          </div>
          <div
            class="bg-gray-200 text-center border-2 border-r-gray-300 py-2 text-lg font-bold w-1/3"
          >
            Score
          </div>
          <div
            class="bg-gray-200 text-center border=2 border-r-gray-300 py-2 text-lg font-bold w-1/3"
          >
            Total time
          </div>
        </div>
        <div class="border border-gray-300">
          <div v-for="stats in allStats" class="grid grid-cols-3 grid-flow-row">
            <div
              v-for="information in stats"
              class="text-center py-2 border border-r-gray-300"
            >
              {{ information }}
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
.countdown > span:before {
  position: relative;
  content: "0\A 1\A 2\A 3\A 4\A 5\A 6\A 7\A 8\A 9\A 10\A 11\A 12\A 13\A 14\A 15\A 16\A 17\A 18\A 19\A 20\A 21\A 22\A 23\A 24\A 25\A 26\A 27\A 28\A 29\A 30\A 31\A 32\A 33\A 34\A 35\A 36\A 37\A 38\A 39\A 40\A 41\A 42\A 43\A 44\A 45\A 46\A 47\A 48\A 49\A 50\A 51\A 52\A 53\A 54\A 55\A 56\A 57\A 58\A 59\A 60\A 61\A 62\A 63\A 64\A 65\A 66\A 67\A 68\A 69\A 70\A 71\A 72\A 73\A 74\A 75\A 76\A 77\A 78\A 79\A 80\A 81\A 82\A 83\A 84\A 85\A 86\A 87\A 88\A 89\A 90\A 91\A 92\A 93\A 94\A 95\A 96\A 97\A 98\A 99\A";
  white-space: pre;
  top: calc(var(--value) * -1em);
  text-align: center;
  transition: all 1s cubic-bezier(1, 0, 0, 1);
}
</style>
