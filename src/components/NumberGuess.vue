<template>
  <div
    class="flex items-center justify-center p-12 bg-gradient-to-br from blue-100 to-indigo-200"
  >
    <div
      class="max-w-md mx-auto bg-white p-8 border rounded-2xl shadow-lg animaate-fade-in"
    >
      <h2 class="text-3xl font-extrabold text-indigo-700 mb-6 text-center">
        Number Guessing Game
      </h2>
      <p class="text-sm text-gray-400 font-semibold mb-6">
        Guess the number between 1 - 50. You have 10 chance to guess the right
        number.
      </p>
      <form @submit.prevent="checkGuess" class="space-y-4">
        <input
          type="number"
          v-model.number="guess"
          class="w-full border border-gray-300 rounded-lg p-3 focus:outline-none focus:ring-indigo-400 transition"
        />
        <button
          type="submit"
          class="bg-indigo-600 text-white py-2 px-4 rounded-lg hover:bg-indigo-700 transition"
        >
          Guess
        </button>
      </form>
      <transition name="fade">
        <p
          class="mt-6 text-center font-medium text-lg text-gray-700"
          v-if="message"
        >
          {{ message }}
        </p>
      </transition>
      <p class="text-sm text-gray-400 text-center">
        {{ guesses?.length }} from 5 guesses
      </p>
      <div v-if="guesses.length" class="mt-6">
        <h3 class="font-semibold mb-2 text-gray-600">Guess history:</h3>
        <TransitionGroup
          name="list"
          tag="ul"
          class="list-disc ml-4 text-gray-700"
        >
          <li v-for="(item, index) in guesses" :key="index">{{ item }}</li>
        </TransitionGroup>
      </div>
      <div class="flex justify-end">
        <button
          @click="resetGame"
          class="mt-6 text-sm text-indigo-500 hover:underline transition"
        >
          Reset
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, TransitionGroup } from "vue";

const generateNumber = () => {
  return Math.floor(Math.random() * 50) + 1;
};
const target = ref(generateNumber());
const guess = ref(null);
const message = ref("");
const guesses = ref([]);

const gameOver = computed(() => {
  return guesses.value.length >= 10 || message.value.includes("Correct");
});

const checkGuess = () => {
  if (guess.value == null || gameOver.value) return;

  guesses.value.push(guess.value);

  if (guess.value > target.value) {
    message.value = "Your answer is too high!";
    guess.value = "";
  } else if (guess.value < target.value) {
    message.value = "Your answer is too low!";
    guess.value = "";
  } else {
    message.value = `Correct! the number is ${target.value}`;
    return;
  }

  if (guesses.value.length >= 5) {
    message.value = `You failed to guess the right number.The right answer is ${target.value}. Nice try by the way.`;
  }
  guess.value = null;
};

const resetGame = () => {
  target.value = generateNumber();
  guess.value = "";
  guesses.value = [];
  message.value = "";
};
</script>

<style scoped>
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0px);
  }
}

.animate-fade-in {
  animation: fade-in 0.6s ease-out;
}

.fade-enter-active,
.fafe-leave-active {
  transition: opacity 0.5s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.list-enter-from {
  opacity: 0;
  transform: translateY(-20px);
}
.list-enter-to {
  opacity: 1;
  transform: translateY(0);
}
.list-enter-active {
  transition: all 0.7s ease;
}
</style>