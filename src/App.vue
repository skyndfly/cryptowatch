<template>
  <div class="add_wrapper">
    <p>Тикер:</p>
    <input v-model="ticker" @keydown.enter="add" type="text" />
    <button @click="add">Добавить</button>
  </div>

  <template v-if="tickers.length">
    <div class="filter_wrapper">
      <p>: Фильтр</p>
      <input v-model="filter" type="text" />
    </div>
    <div class="pagination_wrapper">
      <button>Назад</button>
      <button>Вперед</button>
    </div>
    <div class="added_wrapper">
      <div
        v-for="t in filteredList()"
        :key="t.name"
        :class="{ active: active === t }"
        class="item"
        @click="setTicker(t)"
      >
        <p class="name">{{ t.name }} — USD</p>
        <h2 class="price">{{ t.price }}</h2>
        <button class="delete" @click.stop="deleteTicker(t)">Удалить</button>
      </div>
    </div>
  </template>
  <template v-else>
    <p class="empty">Добавьте валюту для отслеживания</p>
  </template>
  <template v-if="active">
    <div class="graph">
      <button type="button" @click="handleDisabledGraph">X</button>
      <h2>{{ active.name }}</h2>
      <div class="graph_content">
        <div
          v-for="(bar, idx) in drawGraph()"
          :key="idx"
          class="item"
          :style="{ height: `${bar}%` }"
          :title="graph[idx]"
        ></div>
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
      active: null,
      graph: [],
      filter: "",
    };
  },
  created() {
    const tikersData = localStorage.getItem("cryptowatch-list");
    if (tikersData) {
      this.tickers = JSON.parse(tikersData);
      this.tickers.forEach((ticker) => {
        this.updateData(ticker.name);
      });
    }
  },
  methods: {
    filteredList: function () {
      return this.tickers.filter((tisker) => tisker.name.includes(this.filter));
    },
    updateData: function (tickerName) {
      setInterval(async () => {
        const api = await fetch(
          `https://min-api.cryptocompare.com/data/price?fsym=${tickerName}&tsyms=usd`
        );
        const data = await api.json();
        this.tickers.find((t) => t.name === tickerName).price =
          data.USD > 1 ? data.USD.toFixed(2) : data.USD.toPrecision(2);
        if (this.active.name === tickerName) {
          this.graph.push(data.USD);
        }
      }, 3000);
    },
    add() {
      const newTicker = { name: this.ticker, price: "—" };
      this.tickers.push(newTicker);
      localStorage.setItem("cryptowatch-list", JSON.stringify(this.tickers));
      this.updateData(newTicker.name);
      this.ticker = "";
    },
    deleteTicker(ticker) {
      this.active = null;
      this.tickers = this.tickers.filter((t) => t !== ticker);
    },
    handleDisabledGraph() {
      this.active = null;
    },
    drawGraph: function () {
      let max = Math.max(...this.graph);
      let min = Math.min(...this.graph);
      max = max === min ? max + 20 : max;
      let value = this.graph.map(
        (price) => 20 + ((price - min) * 95) / (max - min)
      );
      if (value > 100) {
        value = 100;
      }
      return value;
    },
    setTicker: function (ticker) {
      this.active = ticker;
      this.graph = [];
    },
  },
};
</script>

<style src="./app.css"></style>
