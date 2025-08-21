<template>
  <div
    class="p-12 flex items-center justify-center bg-gradient-to-br from-yellow-100 to-amber-200"
  >
    <div
      class="bg-white p-6 rounded-xl shadow-lg max-w-md w-full animate-fade-in"
    >
      <h2 class="text-2xl font-bold text-center text-amber-700 mb-4">
        🧠 Quiz Game
      </h2>

      <div v-if="!started && !loading" class="text-center">
        <button
          @click="startQuiz"
          class="bg-amber-500 text-white px-6 py-3 rounded-lg text-lg hover:bg-amber-600 transition"
        >
          START
        </button>
      </div>
      <div v-else-if="loading" class="text-center text-gray-600">
        🔄 Getting Task...
      </div>

      <div v-else-if="!quizFinished">
        <p class="mb-2 text-sm text-gray-500 text-right">⏱️ {{ timer }} sec</p>
        <p class="mb-4 text-lg font-semibold text-gray-700">
          {{ currentQuestion.question }}
        </p>

        <div class="grid">
          <transition-group name="scale-fade" tag="div">
            <button
              v-for="(option, index) in currentQuestion.options"
              :key="index"
              @click="selectAnswer(option)"
              class="bg-amber-100 border p-2 m-2 rounded text-left transition"
              :class="{
                'bg-green-300 scale-105':
                  selected === option && isCorrect(option),
                'bg-red-300 scale-105':
                  selected === option && !isCorrect(option),
                'opacity-50': selected && selected !== option,
              }"
              :disabled="selected"
            >
              {{ option }}
            </button>
          </transition-group>
        </div>

        <button
          v-if="selected"
          @click="nextQuestion"
          class="mt-4 w-full bg-amber-500 text-white px-4 py-2 rounded hover:bg-amber-600 transition"
        >
          Next
        </button>
      </div>

      <div v-else class="text-center">
        <p class="text-xl font-semibold text-gray-700 mb-2">
          🎉 Quiz finished!
        </p>
        <p class="text-lg text-gray-600">
          Your score is {{ score }} from {{ questions.length }} quetions.
        </p>
        <button
          @click="resetQuiz"
          class="mt-4 text-amber-600 hover:underline text-sm"
        >
          🔄 Try Again!
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from "vue";

const questions = ref([]);
const currentIndex = ref(0);
const selected = ref(null);
const score = ref(0);
const timer = ref(15);
const timerInterval = ref(null);
const loading = ref(true);
const started = ref(false);

const quizFinished = computed(
  () => currentIndex.value >= questions.value.length
);
const currentQuestion = computed(
  () => questions.value[currentIndex.value] || {}
);

async function fetchQuestions() {
  loading.value = true;
  try {
    const res = await fetch(
      "https://opentdb.com/api.php?amount=5&type=multiple"
    );
    const data = await res.json();
    questions.value = data.results.map((q) => {
      const options = [...q.incorrect_answers, q.correct_answer];
      return {
        question: decodeHTMLEntities(q.question),
        options: shuffleArray(options.map(decodeHTMLEntities)),
        answer: decodeHTMLEntities(q.correct_answer),
      };
    });
  } catch (e) {
    console.error("Gagal memuat soal:", e);
  } finally {
    loading.value = false;
  }
}

const startQuiz = () => {
  started.value = true;
  startTimer();
};

const decodeHTMLEntities = (text) => {
  const txt = document.createElement("textarea");
  txt.innerHTML = text;
  return txt.value;
};

const shuffleArray = (array) => {
  return array.sort(() => Math.random() - 0.5);
};

const isCorrect = (option) => {
  return option === currentQuestion.value.answer;
};

const selectAnswer = (option) => {
  selected.value = option;
  clearInterval(timerInterval.value);
  if (isCorrect(option)) score.value++;
};

const nextQuestion = () => {
  selected.value = null;
  currentIndex.value++;
  timer.value = 15;
  if (!quizFinished.value) startTimer();
};

const startTimer = () => {
  clearInterval(timerInterval.value);
  timerInterval.value = setInterval(() => {
    if (timer.value > 0) {
      timer.value--;
    } else {
      clearInterval(timerInterval.value);
      selected.value = "Waktu Habis";
      nextQuestion();
    }
  }, 1000);
};

const resetQuiz = () => {
  currentIndex.value = 0;
  score.value = 0;
  selected.value = null;
  timer.value = 15;
  fetchQuestions();
};

onMounted(() => {
  fetchQuestions();
});

watch(quizFinished, (finished) => {
  if (finished) clearInterval(timerInterval.value);
});
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
