<template>
  <div class="currency-converter">
    <form class="currency-converter__form">
      <div class="currency-converter__input-group">
        <label class="currency-converter__input-label"></label>
        <div class="currency-converter__select-container">
          <select v-model="fromCurrency" @change="convertCurrency" class="currency-converter__select">
            <option v-for="currency in currencies" :key="currency" :value="currency">{{ currency }}</option>
          </select>
          <input type="number" v-model="fromAmount" @input="convertCurrency" class="currency-converter__input" />
        </div>
      </div>
      <img style="height:50px" src="@/assets/arrow.gif" alt="">
      <div class="currency-converter__input-group">
        <label class="currency-converter__input-label"></label>
        <div class="currency-converter__select-container">
          <select v-model="toCurrency" @change="convertCurrency" class="currency-converter__select">
            <option v-for="currency in currencies" :key="currency" :value="currency">{{ currency }}</option>
          </select>
          <input type="number" v-model="toAmount" @input="convertCurrency" class="currency-converter__input" />
        </div>
      </div>
    </form>
  </div>
</template>
<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";

interface CurrencyRates {
  [key: string]: number;
}

export default defineComponent({
  name: "CurrencyConverter",
  data() {
    return {
      fromCurrency: "USD",
      toCurrency: "BTC",
      fromAmount: 1,
      toAmount: 0,
      baseCurrency: "USD",
      currencyRates: {} as CurrencyRates,
      currencies: ["USD", "EUR", "UAH", "GBP", "BTC", "ETH", "BNB", "XRP"],
      baseCurrencies: ["USD", "EUR", "UAH"],
      errorMessage: '',
    };
  },

  methods: {
    async fetchCurrencyRates() {
      const { data } = await axios.get(
          `https://api.coinbase.com/v2/exchange-rates?currency=${this.baseCurrency}`
      );
      this.currencyRates = data.data.rates;
      await this.convertCurrency();
    },
    async convertCurrency() {
      if (this.fromCurrency === this.toCurrency) {
        this.toAmount = this.fromAmount;
        return;
      }
      const fromRate = this.currencyRates[this.fromCurrency];
      const toRate = this.currencyRates[this.toCurrency];

      const fromCurrencyUSD = await axios.get(`https://api.exchangerate-api.com/v4/latest/${this.fromCurrency}`);
      const fromCurrencyRate = fromCurrencyUSD.data.rates.USD;

      const maxFromAmount = 10000 / fromCurrencyRate;

      if (this.fromAmount > maxFromAmount) {
        this.errorMessage = `The maximum allowed amount for ${this.fromCurrency} is ${maxFromAmount.toFixed(2)} USD.`;
        alert(this.errorMessage);
        return;
      }
      const baseAmount = this.fromAmount / fromRate;
      this.toAmount = baseAmount * toRate;

      if (this.toAmount > 10000) {
        this.errorMessage = 'The maximum allowed amount for the desired currency is 10,000 USD.';
        alert(this.errorMessage);
        this.toAmount = 10000;
        this.fromAmount = (this.toAmount / toRate) * fromRate;
      } else {
        this.errorMessage = '';
      }
    },
  },

  async mounted() {
    await this.fetchCurrencyRates();
  },
});
</script>

