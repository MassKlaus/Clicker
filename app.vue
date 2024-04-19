<template>
  <div class="grid justify-center items-center h-screen " v-if="!gamestate.playing">
    <div>


      <h1 class="text-3xl text-center underline mb-4 w-[250px]">Highscores</h1>
      <div class="grid">
        <div v-for="(score, i) in orderedScores" :key="score.id">
          <span class="select-none">{{ i + 1 }} - {{ timeDisplay(score.ticks) }}</span>
        </div>
      </div>
    </div>

    <div class="flex flex-col">
      <div class="flex gap-3 justify-center text-3xl mb-4">
        <h1>Score:</h1>
        <span>{{ timeDisplay(gamestate.newestScore) }}</span>
      </div>
      <button class="rounded-md bg-pink-300 p-2" @click="startGame">Start Game</button>
    </div>
  </div>
  <div class="flex justify-center items-center h-screen bg-red-100 select-none" @click="clickHandler" v-else>
    <span>{{ gamestate.counter }}</span>
  </div>
</template>

<script setup>

const gamestate = ref({
  counter: 0,
  playing: false,
  startTimer: Date.now(),
  endTimer: Date.now(),
  newestScore: 0,
})

const gamescores = ref([
])

const orderedScores = computed(() => {
  return gamescores.value.sort((a, b) => a.ticks - b.ticks);
})

function startGame() {
  gamestate.value.playing = true;
  gamestate.value.counter = 0;
}

function endGame() {
  gamestate.value.playing = false;
  gamestate.value.endTimer = Date.now();

  const score = {
    id: gamescores.value.length + 1,
    ticks: gamestate.value.endTimer - gamestate.value.startTimer,
  }

  gamescores.value.push(score);
  gamestate.value.newestScore = score.ticks;

  //save to local storage
  localStorage.setItem('gamescores', JSON.stringify(gamescores.value));
}

function clickHandler() {
  if (!gamestate.value.playing) {
    return;
  }

  if (gamestate.value.counter === 0) {
    gamestate.value.startTimer = Date.now();
  }

  gamestate.value.counter++;

  if (gamestate.value.counter === 10) {
    endGame();
  }
}

function timeDisplay(time) {
  return `${(time / 1000).toFixed(3)}s`;
}

onMounted(() => {
  const scores = localStorage.getItem('gamescores');
  if (scores) {
    gamescores.value = JSON.parse(scores);
  }
})

</script>


<style>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100vw;
}
</style>
