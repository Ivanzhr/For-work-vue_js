<template>
  <div class="quote-container" @click="copyToClipboard({ quote, author })">
    <p class="quote-text">{{ quote }}</p>
    <p class="quote-author">— {{ author }}</p>
  </div>
  <button @click="fetchRandomQuote">Завантажити іншу цитату</button>
  <div class="share-buttons">
    <h3>Поширити цитату:</h3>
    <button @click="shareOnTelegram">Telegram</button>
  </div>

  <div class="settings">
    <h3>Налаштування цитати</h3>
    <label>
      Виберіть категорію:
      <select v-model="selectedCategory">
        <option value="">Всі</option>
        <option value="inspire">Натхнення</option>
        <option value="life">Життя</option>
        <option value="love">Кохання</option>
        <option value="management">Управління</option>
        <option value="sports">Спорт</option>
        <option value="funny">Веселі</option>
        <option value="art">Арт</option>
      </select>
    </label>
  </div>

  <div class="quote-history">
    <h3>Історія цитат</h3>
    <ul>
      <li v-for="(item, index) in dataHistory" :key="index" @click="copyToClipboard(item)" class="quote-item">
        <p class="quote-text">{{ item.quote }}</p>
        <p class="quote-author">— {{ item.author }}</p>
      </li>
    </ul>
  </div>

  <div v-if="showCopyMessage" class="copy-message">
    Цитату скопійовано в буфер обміну!
  </div>
</template>

<script>
export default {
  data() {
    return {
      quote: '',
      author: '',
      dataHistory: [],
      showCopyMessage: false,
      selectedCategory: '', // Вибрана категорія
    };
  },
  methods: {
    async fetchRandomQuote() {
      let url 

      if(this.selectedCategory) {
        url = `https://quotes.rest/qod.json?api_key=EISUF917UvIEUbL08VOHTa3sUHciFt8k3VfpDxj4a7eee567&category=${this.selectedCategory}`
      }
      else {
        url = `https://quotes.rest/quote/random.json?api_key=EISUF917UvIEUbL08VOHTa3sUHciFt8k3VfpDxj4a7eee567`
      }
      try {
        const response = await fetch(url);
        const data = await response.json();
        console.log(response)
        
        const newQuote = data.contents.quotes[0].quote || 'Цитата не знайдена';
        const newAuthor = data.contents.quotes[0].author || 'Автор невідомий';
        
        this.quote = newQuote;
        this.author = newAuthor;
        this.dataHistory.push({ quote: newQuote, author: newAuthor });
      } catch (error) {
        console.error('Помилка при завантаженні цитати:', error);
        this.quote = 'Не вдалося завантажити цитату';
        this.author = '';
      }
    },
    copyToClipboard(item) {
      const quoteText = `${item.quote} — ${item.author}`;
      navigator.clipboard.writeText(quoteText).then(() => {
        this.showCopyMessage = true;
        setTimeout(() => {
          this.showCopyMessage = false;
        }, 3000);
      }).catch((error) => {
        console.error('Помилка копіювання:', error);
      });
    },
    shareOnTelegram() {
    const text = encodeURIComponent(`${this.quote} — ${this.author}`);
    const url = `https://t.me/share/url?url=${window.location.href}&text=${text}`;
    window.open(url, '_blank');
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
  cursor: pointer;
  padding: 20px;
  border: 1px solid #ccc;
  transition: background-color 0.3s;
}

.quote-container:hover {
  background-color: #f9f9f9;
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

.settings {
  margin: 20px 0;
  text-align: center;
}

.settings label {
  margin: 0 10px;
}

.share-buttons {
  text-align: center;
  margin: 20px 0;
}

.share-buttons button {
  margin: 0 10px;
  padding: 8px 15px;
  cursor: pointer;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.share-buttons button:hover {
  background-color: #0056b3;
}

.quote-history {
  margin-top: 30px;
}

.quote-history h3 {
  text-align: center;
}

.quote-history ul {
  list-style-type: none;
  padding: 0;
}

.quote-item {
  margin-bottom: 15px;
  cursor: pointer;
  padding: 10px;
  transition: background-color 0.3s;
}

.quote-item:hover {
  background-color: #f0f0f0;
}

.copy-message {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background-color: #4caf50;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 0.9em;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
  transition: opacity 0.3s ease;
}
</style>
