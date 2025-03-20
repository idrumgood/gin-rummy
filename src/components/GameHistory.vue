<template>
    <div class="mt-12">
      <h2 class="text-2xl font-semibold text-gray-800 text-center mb-6">Game History</h2>
      <div v-if="completedGames.length === 0" class="text-center text-gray-500">No games played yet.</div>
      <div v-else class="space-y-4">
        <div v-for="(game, index) in completedGames" :key="index" class="p-4 bg-gray-50 rounded-lg shadow-md">
          <h3 class="text-lg font-semibold text-blue-600">Game {{ index + 1 }}</h3>
          <p class="text-gray-700">Players: {{ game.player1 }}, {{ game.player2 }}</p>
          <p class="text-gray-700">Winner: <span class="font-semibold">{{ game.winner }}</span></p>
          <p class="text-gray-700">Final Scores: {{ game.player1 }} - {{ game.player1Total }}, {{ game.player2 }} - {{ game.player2Total }}</p>
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
}
</script>