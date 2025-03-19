<template>
  <div class="bg-white rounded-lg shadow-md p-8 w-full max-w-md transition-transform hover:scale-105">
    <h1 class="text-2xl font-semibold text-blue-600 text-center mb-6">Gin Rummy Score Tracker</h1>
    <form @submit="handleSubmit" class="space-y-4">
      <div>
        <label for="player1" class="block text-gray-700 text-sm font-bold mb-2">Player 1</label>
        <input v-model="player1" id="player1" placeholder="Your Name" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
      </div>
      <div>
        <label for="player2" class="block text-gray-700 text-sm font-bold mb-2">Player 2</label>
        <input v-model="player2" id="player2" placeholder="Partner's Name" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
      </div>
      <div>
        <label for="winner" class="block text-gray-700 text-sm font-bold mb-2">Winner</label>
        <select v-model="winner" id="winner" required
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
          <option value="">Select Winner</option>
          <option v-if="player1" :value="player1">{{ player1 }}</option>
          <option v-if="player2" :value="player2">{{ player2 }}</option>
        </select>
      </div>
      <div>
        <label for="score" class="block text-gray-700 text-sm font-bold mb-2">Score</label>
        <input v-model.number="score" type="number" id="score" placeholder="Enter Score" required
               class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
      </div>
      <button type="submit"
              class="bg-gradient-to-r from-green-400 to-blue-500 hover:from-green-500 hover:to-blue-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline w-full transition duration-300 ease-in-out">
        Record Score
      </button>
    </form>
    <div :class="['mt-6 text-center font-medium', messageType === 'success' ? 'text-green-600' : 'text-red-600']">
      {{ message }}
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  name: 'ScoreForm',
  setup() {
    const player1 = ref('');
    const player2 = ref('');
    const winner = ref('');
    const score = ref(null);
    const message = ref('');
    const messageType = ref('success');

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

      // Here, you would typically send the data to a server using fetch() or similar.
      // For this example, we'll just simulate a successful submission.
      console.log({
        player1: player1.value,
        player2: player2.value,
        winner: winner.value,
        score: score.value,
      });

      // Simulate a successful submission (replace with actual server call)
      showMessage('Score recorded successfully!', 'success');
      resetForm();
    };

    const showMessage = (msg, type = 'success') => {
      message.value = msg;
      messageType.value = type;
    };

    const resetForm = () => {
      player1.value = '';
      player2.value = '';
      winner.value = '';
      score.value = null;
    };

    // Watch for changes in player names to update the dropdown
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
      populateWinnerDropdown
    };
  },
};
</script>

<style scoped>
/* You can add scoped styles here if needed */
</style>
