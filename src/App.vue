<script setup>
import { ref, watch } from 'vue'

const userName = ref('') // Username input value
const isError = ref(false) // Check if the input is empty
const handleSubmit = () => { // Submit the form
  if (!userName.value.trim()) {
    isError.value = true
  } else {
    isError.value = false
    console.log("UserName:", userName.value)
  }
}
watch(userName, (newValue) => { // Watch input Username 
  if (newValue.trim()) {
    isError.value = false
  }
})
const isVisible = ref(true) // Show or hide the main page
const toggleVisibility = () => {
  isVisible.value = !isVisible.value

}
</script>

<template>

  <main v-show="isVisible" class="min-h-screen flex items-center justify-center bg-white text-black font-mono">
    <div class="justify-center text-center flex flex-col items-center" id="box1">
      <h1 class="text-7xl font-bold m-2 text-blue-500 -tracking-tighter" id="h1Topic">TORKUM</h1>
      <h2 class="text-xl font-bold m-2">Word Chain Game</h2>
      <p class="text-sm m-2">
        Word Chain is an exciting word game where each new word must begin with the final letter of the preceding word. Challenge your vocabulary skills and see how long of a chain you can create!
      </p> 
      <div class="m-2 flex flex-col items-center justify-center" id="box2">
        <form @submit.prevent="handleSubmit">
          <input
            v-model.trim="userName"
            type="text"
            placeholder="Please enter your name"
            class="input input-bordered w-full drop-shadow-md m-2 mb-4 transition"
            :class="{ 'border-red-500 placeholder-red-500': isError, 'border-gray-300 placeholder-gray-400': !isError }"
            id="inputName"
          />
          <button @click="toggleVisibility" type="submit" class="btn btn-info btn-wide drop-shadow-md m-2 " id="btnPlay">Play!!</button>
        </form>
      </div>
    </div>
  </main>

</template>

<style scoped></style>