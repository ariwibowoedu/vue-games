

<template>
  <div class="max-w-xl md:max-w-md mx-auto px-2">
    <h1 class="text-3xl font-extrabold text-center mt-8">
      Welcome to <span class="text-amber-500">'Game Land'</span>
    </h1>
    <div class="flex flex-row justify-between items-center gap-1 mt-3 mb-2">
      <button
        class="bg-green-400 text-white px-6 py-3 rounded-lg text-lg hover:bg-green-500 transition"
        @click="showGame('tictactoe')"
      >
        Tic Tac Toe
      </button>
      <button
        class="bg-amber-400 text-white px-6 py-3 rounded-lg text-lg hover:bg-amber-500 transition"
        @click="showGame('guess')"
      >
        Guess Number
      </button>
      <button
        class="bg-red-700 text-white px-6 py-3 rounded-lg text-lg hover:bg-red-800 transition"
        @click="showGame('quiz')"
      >
        Quiz Game
      </button>
    </div>
    <hr class="my-2" />
  </div>
  <div v-if="activeComponent">
    <component :is="activeComponent"></component>
  </div>
  <p v-else class="text-center text-gray-500 mt-6">
    Which game you want to play?
  </p>
  <footer class="max-w-xl mx-auto text-center mt-12">
    <p class="text-md text-gray-600">
      &copy;
      <a href="https://ariwibowoedu.github.io" target="_blank">
        ariwibowoedu.github.io
      </a>
    </p>
  </footer>
</template>


<script setup>
import QuizGame from "./components/QuizGame.vue";
import TicTacToe from "./components/TicTacToe.vue";
import NumberGuess from "./components/NumberGuess.vue";

import { ref } from "vue";

const activeComponent = ref(null);

const showGame = (game) => {
  if (game === "quiz") activeComponent.value = QuizGame;
  if (game === "guess") activeComponent.value = NumberGuess;
  if (game === "tictactoe") activeComponent.value = TicTacToe;
};
</script>

<style scoped>
@keyframes fade-in {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
.animate-fade-in {
  animation: fade-in 0.4s ease-out;
}

.scale-fade-enter-active {
  transition: all 0.3s ease;
}
.scale-fade-enter-from {
  transform: scale(0.9);
  opacity: 0;
}
.scale-fade-enter-to {
  transform: scale(1);
  opacity: 1;
}
</style>