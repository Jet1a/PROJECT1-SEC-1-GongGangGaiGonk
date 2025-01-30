<script setup>
import { ref, watch } from "vue";

const userName = ref("");
const isError = ref(false);
const isVisible = ref(true);

const handleSubmit = () => {
  if (!userName.value.trim()) {
    isError.value = true;
  } else {
    isError.value = false;
    isVisible.value = false;
    console.log("UserName:", userName.value);
  }
};

watch(userName, (newValue) => {
  if (newValue.trim()) {
    isError.value = false;
  }
});
</script>

<template>
  <main
    class="min-h-screen flex items-center justify-center bg-white text-black font-mono"
  >
    <div
      v-show="isVisible"
      class="flex flex-col items-center justify-center text-center px-4 sm:px-6 md:px-8"
    >
      <div class="flex flex-col items-center">
        <h1 class="text-[72px] font-bold text-[#1882FF] -tracking-[-0.1em]">
          TORKUM
        </h1>
        <h2 class="text-[24px] font-bold">Word Chain Game</h2>
        <p class="text-sm mt-2 max-w-md">
          Word Chain is an exciting word game where each new word must begin
          with the final letter of the preceding word. Challenge your vocabulary
          skills and see how long of a chain you can create!
        </p>
      </div>

      <div class="mt-6 w-full max-w-md">
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
            class="w-full max-w-[294px] h-14 bg-[#1882FF] text-white font-semibold rounded-lg drop-shadow-md hover:bg-blue-600 transition"
          >
            Play!!
          </button>
        </form>
      </div>
    </div>
  </main>
</template>

<style scoped></style>
