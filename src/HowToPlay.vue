<script setup>
import { ref } from 'vue'
const description = [
    {
        step: 1,
        keyWord: 'starting word',
        title: 'Enter a starting word',
        img: '/src/images/enterWord.png',
        content: 'Begin the game by typing a word into the input field and pressing Enter. This will be your starting word.'
    },
    {
        step: 2,
        keyWord: 'connecting',
        title: 'Connecting word',
        img: '/src/images/connect.png',
        content: 'Enter words that start with the last letter of the previous word you enter. Each word must be unique and you have 15 seconds to enter each word. If you don’t enter a word in time, the game will end.'
    },
    {
        step: 3,
        keyWord: 'repeat word',
        title: 'Repeat until beat the game!',
        img: '/src/images/repeat.png',
        content: 'Do the same step as mention before. The game will continues until you can’t find a new word that starts with the last letter of the previous word or until the timer run out. '
    }

]
const counter = ref(0)
const increment = () => {
    if (counter.value < description.length - 1) {
        counter.value++
    }
    else if (counter.value === description.length - 1) {
        isVisible.value = false
    }
    console.log(`increment : ${counter.value}`)
}

const decrement = () => {
    if (counter.value > 0) {
        counter.value--
    }
    console.log(`decrement : ${counter.value}`)
}
const isVisible = ref(true)

</script>

<template>
    <div class="bg-white min-h-screen">
        <div v-show="isVisible">
            <div class="bg-white">
                <div class="grid w-full min-h-screen place-items-center py-9">
                    <div class="w-full">
                        <div class="flex justify-center mb-9">
                            <ul class="steps steps-horizontal">
                                <li v-for="(step, index) in description" :key="index" class="step"
                                    :class="{ 'mx-auto sm:mx-2 step-primary after:!bg-[#1882FF] before:!bg-[#1882FF] after:!text-white': counter >= index }">
                                    {{ step.keyWord }}
                                </li>
                            </ul>
                        </div>
                        <div class="hero-content text-center border-2 rounded-xl border-gray-400 p-[40px]
                                w-[50%] h-[30rem] mx-auto bg-white shadow-lg ">
                            <div class="max-w-md mx-auto">

                                <h1 class="text-5xl font-bold text-[#1882FF] my-3">How to Play ?</h1>
                                <img :src="`${description[counter].img}`" alt="how-to-play"
                                    class="w-[70%] mx-auto my-6">
                                <p class="text-black font-semibold	">{{ description[counter].title }}</p>
                                <p class="py-2 mb-8 text-gray-500">
                                    {{ description[counter].content }}
                                </p>
                            </div>
                        </div>
                        <div class="gap-4 grid grid-cols-2 max-w-[30%] mx-auto mt-9">
                            <button class="btn bg-[#D9D9D9] text-[#595959] border-0" @click="decrement"
                                :disabled="counter === 0">
                                <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true"
                                    xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor"
                                    viewBox="0 0 24 24">
                                    <path fill-rule="evenodd"
                                        d="M13.729 5.575c1.304-1.074 3.27-.146 3.27 1.544v9.762c0 1.69-1.966 2.618-3.27 1.544l-5.927-4.881a2 2 0 0 1 0-3.088l5.927-4.88Z"
                                        clip-rule="evenodd" />
                                </svg>
                                Back
                            </button>
                            <button class="btn bg-[#1882FF] border-0 text-white" @click="increment">
                                Next
                                <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true"
                                    xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor"
                                    viewBox="0 0 24 24">
                                    <path fill-rule="evenodd"
                                        d="M10.271 5.575C8.967 4.501 7 5.43 7 7.12v9.762c0 1.69 1.967 2.618 3.271 1.544l5.927-4.881a2 2 0 0 0 0-3.088l-5.927-4.88Z"
                                        clip-rule="evenodd" />
                                </svg>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
