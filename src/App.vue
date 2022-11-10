<template>
  <div class="add_wrapper">
    <p>Тикер:</p>
    <input v-model="ticker" @keydown.enter="add" type="text" />
    <button @click="add">Добавить</button>
  </div>

  <template v-if="tickers.length">
    <div class="added_wrapper">
      <div
        v-for="t in tickers"
        :key="t.name"
        :class="{ active: active === t }"
        class="item"
        @click="active = t"
      >
        <p class="name">{{ t.name }}</p>
        <h2 class="price">{{ t.price }}</h2>
        <button class="delete" @click.stop="deleteTicker(t)">Удалить</button>
      </div>
    </div>
  </template>
  <template v-if="active">
    <div class="graph">
      <button type="button" @click="handleDisabledGraph">X</button>
      <h2>{{ active.name }}</h2>
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
      active: null,
    };
  },
  methods: {
    add() {
      const newTicker = { name: this.ticker, price: 11 };
      setInterval(async () => {
        const api = await fetch(
          `https://min-api.cryptocompare.com/data/price?fsym=${newTicker.name}&tsyms=usd`
        );
        const data = await api.json();
        console.log(data);
      }, 3000);
      this.tickers.push(newTicker);
      this.ticker = "";
    },
    deleteTicker(ticker) {
      this.active = null;
      this.tickers = this.tickers.filter((t) => t !== ticker);
    },
    handleDisabledGraph() {
      this.active = null;
    },
  },
};
</script>

<style src="./app.css"></style>
