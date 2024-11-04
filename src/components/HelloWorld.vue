<template>
  <div class="quote-container">
    <p class="quote-text">{{ quote }}</p>
    <p class="quote-author">— {{ author }}</p>
    <button @click="fetchRandomQuote">Завантажити іншу цитату</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      quote: '',
      author: '',
    };
  },
  methods: {
    async fetchRandomQuote() {
      try {
        const response = await fetch('https://quotes.rest/quote/random.json?api_key=EISUF917UvIEUbL08VOHTa3sUHciFt8k3VfpDxj4a7eee567');
        const data = await response.json();
        
        // Оновлення полів цитати та автора згідно зі структурою відповіді
        this.quote = data.contents.quotes[0].quote || 'Цитата не знайдена';
        this.author = data.contents.quotes[0].author || 'Автор невідомий';
        
        console.log(data); // Для перевірки структури відповіді
      } catch (error) {
        console.error('Помилка при завантаженні цитати:', error);
        this.quote = 'Не вдалося завантажити цитату';
        this.author = '';
      }
    },
  },
  mounted() {
    this.fetchRandomQuote();
  },
};
</script>

<style scoped>
.quote-container {
  text-align: center;
  margin: 20px;
}

.quote-text {
  font-size: 1.5em;
  margin-bottom: 10px;
}

.quote-author {
  font-style: italic;
  color: #555;
  margin-bottom: 20px;
}

button {
  padding: 10px 20px;
  font-size: 1em;
  cursor: pointer;
}
</style>
