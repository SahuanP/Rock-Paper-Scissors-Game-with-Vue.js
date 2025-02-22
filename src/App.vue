<script setup>
import { ref, computed, onMounted } from "vue";

const wins = ref(0);
const draws = ref(0);
const losses = ref(0);

const choice = ref(null);
const computerChoice = ref(null);
const verdict = ref(null);

const outcomes = {
  rock: {
    rock: "draw",
    paper: "lose",
    scissors: "win",
  },
  paper: {
    rock: "win",
    paper: "draw",
    scissors: "lose",
  },
  scissors: {
    rock: "lose",
    paper: "win",
    scissors: "draw",
  },
};

const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value;
  return total ? (wins.value / total) * 100 : 0;
});

const play = (c) => {
  choice.value = c;

  const choices = ["rock", "paper", "scissors"];
  const random = Math.floor(Math.random() * choices.length);
  computerChoice.value = choices[random];

  const outcome = outcomes[c][computerChoice.value];

  if (outcome === "win") {
    wins.value++;
    verdict.value = "Você venceu!";
  } else if (outcome === "loss") {
    losses.value++;
    verdict.value = "Você perdeu!";
  } else {
    draws.value++;
    verdict.value = "Deu empate!";
  }

  SaveGame();
};

const SaveGame = () => {
  localStorage.setItem("wins", wins.value);
  localStorage.setItem("draws", draws.value);
  localStorage.setItem("losses", losses.value);
};

const LoadGame = () => {
  wins.value = localStorage.getItem("wins") || 0;
  draws.value = localStorage.getItem("draws") || 0;
  losses.value = localStorage.getItem("losses") || 0;
};

const ResetRound = () => {
  choice.value = null;
  computerChoice.value = null;
  verdict.value = null;
};

onMounted(() => {
  LoadGame();
  window.addEventListener("keypress", (e) => {
    if (e.key === "r") {
      ResetRound();
    }
  });
});
</script>

<template>
  <div class="bg-gray-700 text-white text-center min-h-screen flex flex-col">
    <header class="container mx-auto p-6">
      <h1 class="text-4xl font-bold">Rock, Paper, Scissors</h1>
    </header>
    <main class="container mx-auto p-6 flex-1">
      <div v-if="choice === null" class="flex items-center justify-center mx-6">
        <button
          @click="play('rock')"
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-pink-500"
        >
          <img src="./rock.svg" alt="Rock" class="w-full" />
        </button>

        <button
          @click="play('paper')"
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-green-500"
        >
          <img src="./paper.svg" alt="Paper" class="w-full" />
        </button>

        <button
          @click="play('scissors')"
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-yellow-500"
        >
          <img src="./scissors.svg" alt="Scissors" class="w-full" />
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          Você escolheu <span class="text-pink-500"> {{ choice }}</span>
        </div>

        <div class="text-3xl mb-4">
          O computador escolheu
          <span class="text-green-500"> {{ computerChoice }}</span>
        </div>

        <div class="text-6xl mb-12">
          {{ verdict }}
        </div>

        <button @click="ResetRound" class="bg-pink-500 text-lg py-2 px-4">
          Reset
        </button>
      </div>

      <div class="mt-12 text-3xl mb-4">
        {{ wins }} vitórias : {{ draws }} empates : {{ losses }} derrotas
      </div>

      <div class="text-lg">Win rate: {{ Math.round(winPercentage) }}%</div>
    </main>
  </div>
</template>
