<script setup>
import {ref} from 'vue'
const score = ref(0)
const totalTime = ref(0)
const wordCount = ref(0)
</script>

<template>
  <main
    class="min-h-screen flex items-center justify-center text-black font-mono"
  >
    <section class="w-[700px]">
      <div><button class="text-2xl font-bold border border-gray-200 rounded-md px-8 py-0.5 shadow-md">Back</button></div>
      <div class="border border-gray-200">
        <p class="text-center text-2xl font-bold py-5">Game Over</p>
        <div class="flex justify-around">
          <div class="flex flex-col items-center">
            <p class="text-2xl">Score</p>
            <p class="text-5xl">{{score}}</p>
          </div>
          <div class="flex flex-col items-center">
            <p class="text-2xl">Total Time</p>
            <p class="text-5xl">{{totalTime}}</p>
          </div>
          <div class="flex flex-col items-center">
            <p class="text-2xl">Word Count</p>
            <p class="text-5xl">{{wordCount}}</p>
          </div>
        </div>
      </div>
      <div>
        <p>Used word</p>
      </div>
    </section>
  </main>
</template>

<style scoped></style>
