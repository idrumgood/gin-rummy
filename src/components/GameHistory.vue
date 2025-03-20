<template>
  <div class="mt-12">
    <h2 class="text-2xl font-semibold text-gray-200 text-center mb-6">Game History</h2>
    <div v-if="completedGames.length === 0" class="text-center text-gray-400">No games played yet.</div>
    <div v-else class="space-y-4">
      <div v-for="(game, index) in completedGames" :key="index"
           class="p-4 bg-gray-700 rounded-lg shadow-md border border-gray-600">
        <h3 class="text-lg font-semibold text-blue-300">Game {{ index + 1 }}</h3>
        <p class="text-gray-300">Players: {{ game.player1 }}, {{ game.player2 }}</p>
        <p class="text-gray-300">Winner: <span class="font-semibold text-green-400">{{ game.winner }}</span></p>
        <p class="text-gray-300">Final Scores: {{ game.player1 }} - {{ game.player1Total }}, {{ game.player2 }}
          - {{ game.player2Total }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameHistory',
  props: {
    completedGames: {
      type: Array,
      required: true,
    },
  },
};
</script>
