<template>
  <div
    class="py-12 flex items-center justify-center bg-gradient-to-br from-green-100 to-emerald-200"
  >
    <div
      class="mx-4 bg-white p-6 rounded-xl shadow-lg max-w-md w-full animate-fade-in"
    >
      <h2 class="text-2xl font-bold text-center text-emerald-700 mb-4">
        Tic Tac Toe
      </h2>
      <div class="grid grid-cols-3 gap-2 mb-4">
        <button
          v-for="(cell, index) in board"
          :key="index"
          class="h-20 text-3xl font-bold border rounded-lg hover:bg-emerald-100 transition"
          :disabled="cell || winner"
          @click="makeMove(index)"
        >
          {{ cell }}
        </button>
      </div>

      <p class="text-center text-2xl font-medium text-gray-700" v-if="winner">
        {{
          winner === "Draw"
            ? "  Draw!"
            : winner === "X"
            ? "🎉 You Win!"
            : "🎉 Ai Win!"
        }}
      </p>

      <p class="text-center text-2xl font-semibold text-gray-600" v-else>
        {{ currentPlayer === "X" ? "You" : "Ai" }} make move!
      </p>

      <button
        @click="resetGame"
        class="mt-4 block mx-auto text-emerald-600 hover:underline text-sm"
      >
        Try Again
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const board = ref(Array(9).fill(null));
const currentPlayer = ref("X");
const winner = ref(null);

const makeMove = (index) => {
  if (board.value[index] || winner.value) return;

  board.value[index] = "X";
  checkWinner();
  if (!winner.value) {
    currentPlayer.value = "O";
    setTimeout(() => aiMove(), 300);
  }
};

const aiMove = () => {
  const emptyIndices = board.value
    .map((cell, i) => (cell === null ? i : null))
    .filter((i) => i !== null);

  if (emptyIndices.length === 0) return;

  const randomIndex =
    emptyIndices[Math.floor(Math.random() * emptyIndices.length)];

  board.value[randomIndex] = "O";
  checkWinner();

  if (!winner.value) currentPlayer.value = "X";
};

const checkWinner = () => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  for (const [a, b, c] of lines) {
    if (
      board.value[a] &&
      board.value[a] === board.value[b] &&
      board.value[a] === board.value[c]
    ) {
      winner.value = board.value[a];
      return;
    }
  }
  if (board.value.every((cell) => cell)) {
    winner.value = "Draw";
  }
};

const resetGame = () => {
  board.value = Array(9).fill(null);
  winner.value = null;
  currentPlayer.value = "X";
};
</script>
