<template>
  <div class="bg-white rounded-lg shadow-md p-8 w-full max-w-2xl transition-transform hover:scale-105">
    <h1 class="text-2xl font-semibold text-blue-600 text-center mb-6">Gin Rummy Score Tracker</h1>
    <form @submit="handleSubmit" class="space-y-4" :disabled="gameOver">
      <div>
        <label for="player1" class="block text-gray-700 text-sm font-bold mb-2">Player 1</label>
        <input v-model="player1" id="player1" placeholder="Your Name" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" :disabled="gameOver">
      </div>
      <div>
        <label for="player2" class="block text-gray-700 text-sm font-bold mb-2">Player 2</label>
        <input v-model="player2" id="player2" placeholder="Partner's Name" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" :disabled="gameOver">
      </div>
      <div>
        <label for="winner" class="block text-gray-700 text-sm font-bold mb-2">Winner</label>
        <select v-model="winner" id="winner" required
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" :disabled="gameOver">
          <option value="">Select Winner</option>
          <option v-if="player1" :value="player1">{{ player1 }}</option>
          <option v-if="player2" :value="player2">{{ player2 }}</option>
        </select>
      </div>
      <div>
        <label for="score" class="block text-gray-700 text-sm font-bold mb-2">Score</label>
        <input v-model.number="score" type="number" id="score" placeholder="Enter Score" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" :disabled="gameOver">
      </div>
      <button type="submit"
              class="bg-gradient-to-r from-green-400 to-blue-500 hover:from-green-500 hover:to-blue-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline w-full transition duration-300 ease-in-out" :disabled="gameOver">
        Record Score
      </button>
    </form>
    <div class="mt-8">
      <h2 class="text-lg font-semibold text-gray-800 text-center mb-4">Total Scores</h2>
      <div class="flex justify-around">
        <div>
          <p class="text-gray-700 font-medium">{{ player1 }}:</p>
          <p :class="['text-lg font-bold', player1Total >= 100 ? 'text-red-600' : 'text-green-600']">{{ player1Total }}</p>
        </div>
        <div>
          <p class="text-gray-700 font-medium">{{ player2 }}:</p>
          <p :class="['text-lg font-bold', player2Total >= 100 ? 'text-red-600' : 'text-green-600']">{{ player2Total }}</p>
        </div>
      </div>
    </div>
    <div v-if="gameOver" class="mt-6 text-center text-xl font-bold text-indigo-700">
      Game Over! Winner: {{ winningPlayer }}
      <button @click="startNewGame" class="mt-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
        Start New Game
      </button>
    </div>
    <div :class="['mt-6 text-center font-medium', messageType === 'success' ? 'text-green-600' : 'text-red-600']">
      {{ message }}
    </div>

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
  </div>
</template>

<script>
import { ref, watch, computed } from 'vue';

export default {
  name: 'ScoreForm',
  setup() {
    const player1 = ref('');
    const player2 = ref('');
    const winner = ref('');
    const score = ref(null);
    const message = ref('');
    const messageType = ref('success');
    const gameOver = ref(false);
    const winningPlayer = ref('');
    const gameNumber = ref(0);
    const scores = ref([]); // Store all rounds of a game
    const completedGames = ref([]); // Store completed games
    const player1Total = ref(0);
    const player2Total = ref(0);

    const populateWinnerDropdown = () => {
      if (!player1.value || !player2.value) return;
      winner.value = '';
    };

    const handleSubmit = (event) => {
      event.preventDefault();

      if (!player1.value || !player2.value) {
        showMessage('Please enter both player names.', 'error');
        return;
      }
      if (!winner.value) {
        showMessage('Please select the winner.', 'error');
        return;
      }
      if (!score.value || score.value <= 0) {
        showMessage('Please enter a valid score.', 'error');
        return;
      }

      // Update total scores
      if (winner.value === player1.value) {
        player1Total.value += score.value;
      } else if (winner.value === player2.value) {
        player2Total.value += score.value;
      }

      // Increment game number (for rounds, not full games)
      gameNumber.value++;

      // Store the round score
      scores.value.push({
        gameNumber: gameNumber.value,
        player1: player1.value,
        player2: player2.value,
        winner: winner.value,
        score: score.value,
        player1Total: player1Total.value,
        player2Total: player2Total.value,
      });

      // Check for game over condition
      if (player1Total.value >= 100 || player2Total.value >= 100) {
        gameOver.value = true;
        winningPlayer.value = player1Total.value >= 100 ? player1.value : player2.value;
        showMessage(`Game Over! Winner: ${winningPlayer.value}`, 'success');

        // Store the completed game
        completedGames.value.push({
          player1: player1.value,
          player2: player2.value,
          winner: winningPlayer.value,
          player1Total: player1Total.value,
          player2Total: player2Total.value,
        });
      } else {
        showMessage('Score recorded successfully!', 'success');
        resetForm();
      }

      // Here, you would typically send the data to a server using fetch() or similar.
      console.log({
        gameNumber: gameNumber.value,
        player1: player1.value,
        player2: player2.value,
        winner: winner.value,
        score: score.value,
        player1Total: player1Total.value,
        player2Total: player2Total.value,
        gameOver: gameOver.value,
        winningPlayer: winningPlayer.value,
        scores: scores.value,
        completedGames: completedGames.value
      });
    };

    const showMessage = (msg, type = 'success') => {
      message.value = msg;
      messageType.value = type;
    };

    const resetForm = () => {
      winner.value = '';
      score.value = null;
    };

    const startNewGame = () => {
      // Reset all game-related state, but keep player names
      player1Total.value = 0;
      player2Total.value = 0;
      gameOver.value = false;
      winningPlayer.value = '';
      gameNumber.value = 0;
      scores.value = []; // Clear round scores, but keep completed games
      message.value = '';
      messageType.value = 'success';

      //Re-enable form,  but keep player names
      winner.value = '';
      score.value = null;
    };

    watch([player1, player2], () => {
      populateWinnerDropdown();
    });

    return {
      player1,
      player2,
      winner,
      score,
      message,
      messageType,
      handleSubmit,
      showMessage,
      resetForm,
      populateWinnerDropdown,
      player1Total,
      player2Total,
      gameOver,
      winningPlayer,
      gameNumber,
      scores,
      completedGames,
      startNewGame
    };
  },
};
</script>

<style scoped>
/* You can add scoped styles here if needed */
</style>
