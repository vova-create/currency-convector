<template>
  <div class="currency-rates">
    <h2 class="currency-rates__title">Your exchange rates</h2>
    <div class="currency-select__option-block">
      <label for="currency-select" class="currency-select__label">Choose a currency:</label>
      <select id="currency-select" class="currency-select__select" v-model="selectedCurrency">
        <option value="USD" class="currency-select__option">USD</option>
        <option value="EUR" class="currency-select__option">EUR</option>
        <option value="UAH" class="currency-select__option">UAH</option>
      </select>
    </div>
    <ul class="currency-list">
      <li v-for="currency in currencies" :key="currency.name" class="currency-list__item">
        <span class="currency-list__name">{{ currency.name }}:</span>
        <span class="currency-list__rate">{{ currency.rate }}</span>
      </li>
    </ul>
  </div>
  <div class="buttons">
    <button class="popup-btn buttons__popup-btn" @click="showPopup = true">+</button>
    <button class="update-btn buttons__update-btn" @click="refreshCurrentRates" :disabled="!isUpdateButtonClickable">
      {{!isUpdateButtonClickable ? refreshButtonText : 'Update rates' }}
    </button>
    <SelectPopUp v-if="showPopup" :options="options" title="Add a new currency" @close="handlePopupClose" @add="handlePopupAdd" />
  </div>
</template>

<script lang="ts" >
import axios from "axios";
import { defineComponent } from "vue";
import SelectPopUp from "@/components/SelectPopUp.vue";



export default defineComponent({
  name: "DisplayCurrentExchangeRatesBlock",
  components: {
    SelectPopUp
  },

  data() {
    return {
      selectedCurrency: "USD",
      currencies: [] as { name: string; rate: number; }[],
      currenciesName: ['USD', 'EUR', 'UAH','RUB', 'AED'],
      showPopup: false,
      options: [] as string[],
      isUpdateButtonClickable: true,
      refreshButtonText: 'You have to wait 5 seconds'

    };
  },
  async mounted() {
    const storedArray = localStorage.getItem('currenciesName');
    if (storedArray) {
      this.currenciesName = JSON.parse(storedArray);
    }
    const response = await axios.get(
        `https://openexchangerates.org/api/currencies.json`
    );
    for (const currency in response.data) {
      this.options.push(currency)
    }
    await this.getCurrencyRates();
  },
  methods: {
    async getCurrencyRates() {
      const rates = [];
      for (const code of this.currenciesName) {
        const response = await axios.get(
            `https://api.exchangerate-api.com/v4/latest/${code}`
        );
        rates.push({
          name: code,
          rate: response.data.rates[this.selectedCurrency].toFixed(3)
        });
      }

      this.currencies = rates;
    },
    handlePopupClose(): void {
      this.showPopup = false;
    },
   async handlePopupAdd(selectedOption: string) {
      this.currenciesName.push(selectedOption);
      await this.getCurrencyRates()
      localStorage.setItem('currenciesName', JSON.stringify(this.currenciesName));
      this.showPopup = false;
    },
  async  refreshCurrentRates() {
    await this.getCurrencyRates()
    setTimeout(() => {
     this.isUpdateButtonClickable = true
   }, 5000);
    this.isUpdateButtonClickable = false
    }
  },
  watch: {
    selectedCurrency() {
      this.getCurrencyRates();
    }
  }
});
</script>

