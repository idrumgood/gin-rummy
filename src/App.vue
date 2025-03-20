<template>
  <div class="bg-gray-900 min-h-screen flex items-center justify-center py-10">
    <div class="bg-gray-800 rounded-xl shadow-2xl p-8 w-full max-w-2xl border border-gray-700">
      <h1 class="font-Fira_Sans_Extra_Condensed text-4xl font-bold text-white uppercase text-center mb-8">
        Gin Rummy Score Tracker
      </h1>
      <form @submit="handleSubmit" class="space-y-6" :disabled="gameOver">
        <div>
          <label for="player1" class="block text-gray-300 text-sm font-bold mb-2">Player 1</label>
          <input v-model="player1" id="player1" placeholder="Your Name" required
                 class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-200 leading-tight focus:outline-none focus:shadow-outline bg-gray-700 border-gray-600 placeholder-gray-400">
        </div>
        <div>
          <label for="player2" class="block text-gray-300 text-sm font-bold mb-2">Player 2</label>
          <input v-model="player2" id="player2" placeholder="Partner's Name" required
                 class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-200 leading-tight focus:outline-none focus:shadow-outline bg-gray-700 border-gray-600 placeholder-gray-400" :disabled="gameOver">
        </div>
        <div>
          <label for="winner" class="block text-gray-300 text-sm font-bold mb-2">Winner</label>
          <select v-model="winner" id="winner" required
                  class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-200 leading-tight focus:outline-none focus:shadow-outline bg-gray-700 border-gray-600" :disabled="gameOver">
            <option value="" class="bg-gray-700 text-gray-400">Select Winner</option>
            <option v-if="player1" :value="player1" class="bg-gray-700 text-white">{{ player1 }}</option>
            <option v-if="player2" :value="player2" class="bg-gray-700 text-white">{{ player2 }}</option>
          </select>
        </div>
        <div>
          <label for="score" class="block text-gray-300 text-sm font-bold mb-2">Score</label>
          <input v-model.number="score" type="number" id="score" placeholder="Enter Score" required
                 class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-200 leading-tight focus:outline-none focus:shadow-outline bg-gray-700 border-gray-600 placeholder-gray-400" :disabled="gameOver">
        </div>
        <button type="submit"
                class="bg-red-500 hover:bg-red-600 text-white font-bold py-3 px-6 rounded-md focus:outline-none focus:shadow-outline w-full transition duration-300 ease-in-out"
                :disabled="gameOver">
          Record Score
        </button>
      </form>
      <div class="mt-10">
        <h2 class="text-2xl font-semibold text-gray-200 text-center mb-6">Total Scores</h2>
        <div class="flex justify-around">
          <div>
            <p class="text-gray-400 font-medium">{{ player1 }}:</p>
            <p :class="['text-xl font-bold', player1Total >= 100 ? 'text-red-400' : 'text-green-400']">{{ player1Total }}</p>
          </div>
          <div>
            <p class="text-gray-400 font-medium">{{ player2 }}:</p>
            <p :class="['text-xl font-bold', player2Total >= 100 ? 'text-red-400' : 'text-green-400']">{{ player2Total }}</p>
          </div>
        </div>
      </div>
      <div v-if="gameOver" class="mt-8 text-center text-2xl font-bold text-indigo-300">
        Game Over! Winner: {{ winningPlayer }}
        <button @click="startNewGame"
                class="mt-6 bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-md focus:outline-none focus:shadow-outline">
          Start New Game
        </button>
      </div>
      <div :class="['mt-8 text-center font-medium', messageType === 'success' ? 'text-green-400' : 'text-red-400']">
        {{ message }}
      </div>
      <GameHistory :completedGames="completedGames" />
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue';
import GameHistory from './components/GameHistory.vue';

export default {
  name: 'ScoreForm',
  components: {
    GameHistory,
  },
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
    const scores = ref([]);
    const completedGames = ref([]);
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

      // Increment game number
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
        completedGames: completedGames.value,
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
      scores.value = [];
      message.value = '';
      messageType.value = 'success';

      //Re-enable form, but keep player names
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
      startNewGame,
    };
  },
};
</script>

