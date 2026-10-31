<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const quote = ref('')
const author = ref('')
const loading = ref(false)
const errorMessage = ref('')

async function getQuote() {
  loading.value = true
  errorMessage.value = ''

  try {
    const response = await axios.get(
      'https://quotes15.p.rapidapi.com/quotes/random/',
      {
        params: { language_code: 'en' },
        headers: {
          'x-rapidapi-host': 'quotes15.p.rapidapi.com',
          'x-rapidapi-key': import.meta.env.VITE_RAPIDAPI_KEY
        }
      }
    )

    quote.value = response.data.content
    author.value = response.data.originator?.name || 'Unknown'
  } catch (error) {
    console.error('Error fetching quote:', error)
    errorMessage.value = 'Unable to load a quote. Please try again.'
  } finally {
    loading.value = false
  }
}

onMounted(getQuote)
</script>

<template>
  <main class="container">
    <h1>Random Quote Generator</h1>

    <div class="quote-box">
      <p v-if="loading">Loading...</p>

      <p v-else-if="errorMessage">
        {{ errorMessage }}
      </p>

      <div v-else>
        <p class="quote">"{{ quote }}"</p>
        <p class="author">- {{ author }}</p>
      </div>
    
      <button @click="getQuote" :disabled="loading">
      {{ loading ? 'loading...' : 'Get New Quote' }}
      </button>
    </div>  
  </main>
</template>

<style>
body {
  background-color: #dde8ed;
  font-family: Arial, sans-serif;
}

.container {
  max-width: 650px;
  margin: 60px auto;
  padding: 30px;
  text-align: center;
  font-family: Arial, sans-serif;
}

h1 {
  color: #244b43;
}

.quote-box {
  background: #f5f8f7;
  padding: 35px 30px;
  margin: 25px auto;
  border-radius: 15px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.12);
  min-height: 250px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}

.quote {
  font-size: 22px;
  font-style: italic;
  line-height: 1.6;
}

button {
  padding: 12px 24px;
  background: #244b43;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  margin-top: auto;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
</style>
