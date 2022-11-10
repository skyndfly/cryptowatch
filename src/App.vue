<template>
  <div class="add_wrapper">
    <p>Тикер:</p>
    <input v-model="ticker" @keydown.enter="add" type="text" />
    <button @click="add">Добавить</button>
  </div>

  <template v-if="tickers.length">
    <div class="added_wrapper">
      <div v-for="t in tickers" :key="t.name" class="item">
        <p class="name">{{ t.name }}</p>
        <h2 class="price">{{ t.price }}</h2>
        <button class="delete" @click="deleteTicker(t)">Удалить</button>
      </div>
    </div>
  </template>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      ticker: null,
      tickers: [],
    };
  },
  methods: {
    add() {
      this.tickers.push({ name: this.ticker, price: 11 });
      this.ticker = "";
    },
    deleteTicker(ticker) {
      this.tickers = this.tickers.filter((t) => t !== ticker);
    },
  },
};
</script>

<style src="./app.css"></style>
