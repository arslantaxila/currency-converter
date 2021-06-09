<template>
  <form @submit.prevent="convert">
    <div class="form-group">
      <label>Value:</label>
      <input
        @keyup="
          ((Object.entries(this.rates).length !== 0) && formValid==true && result.length!==0)
            ? convert()
            : (result = [])
        "
        class="form-control"
        v-model.number="value"
      />
    </div>
    <div class="form-group">
      <label>From:</label>
      <select
        @change="
          ((Object.entries(this.rates).length !== 0) && formValid==true && result.length!==0)
            ? convert()
            : (result = [])
        "
        class="form-control"
        v-model="fromCurrency"
      >
        <option v-for="c of fromCurrencies" :value="c[0]" :key="c[0]">
          {{ c[1] }}
        </option>
      </select>
    </div>
    <div class="form-group">
      <label>To:</label>
      <select
        @change="
          ((Object.entries(this.rates).length !== 0) && formValid==true && result.length!==0)
            ? convert()
            : (result = [])
        "
        class="form-control"
        multiple
        v-model="toCurrency"
      >
        <option v-for="c of toCurrencies" :value="c[0]" :key="c[0]">
          {{ c[1] }}
        </option>
      </select>
    </div>
    <button class="mt-2 btn btn-primary" type="submit">Convert</button>
  </form>
  <div  :class="[result.length==0 ? 'd-none' : '', 'form-group text-center']">
    <h3>{{ value }} {{ fromCurrency }} is:</h3>
    <p v-for="(t,idx) of toCurrency"  :key="t"> {{ this.result[idx] }} {{ t }} </p>
  </div>
</template>

<script>
const currencies = [];
const rates = {};
const toCurrency = [];
const result = [];
export default {
  name: "Converter",
  data() {
    return {
      value: 0,
      fromCurrency: "",
      toCurrency,
      currencies,
      rates,
      result,
    };
  },
  computed: {
    fromCurrencies() {
      const { toCurrency, currencies } = this;
      return currencies.filter((c) => !toCurrency.includes(c[0]));
    },
    toCurrencies() {
      const { fromCurrency, currencies } = this;
      return currencies.filter((c) => c[0] !== fromCurrency);
    },
    formValid() {
      const { value, fromCurrency, toCurrency } = this;
      if (Number.isInteger(value) && value > 0 && fromCurrency=='' && toCurrency.length!==0) return true;
      else if (!Number.isInteger(value)) return 2;
      else if (value <= 0) return 3;
      else if (fromCurrency=='' || toCurrency.length==0) return 4;
      else return 5;
    },
  },
  methods: {
    async convert() {
      this.result = [];
      const { fromCurrency, toCurrency, value, formValid } = this;
      if (formValid == 2) {
        alert("Please enter a number");
        return;
      } else if (formValid == 3) {
        alert("Please add amount greater than zero");
        return;
      } else if (formValid == 4) {
        alert("Please select both currencies");
        return;
      }
      else if (formValid == 5) {
          alert("There is some error");
        return;
      }
      if ((Object.entries(this.rates).length !== 0)) {
         await this.fetchRates();
      }
      if (fromCurrency == "EUR") {
          toCurrency.forEach((val, index) => {
    this.result.splice(index, 0, (value * this.rates[toCurrency[index]]).toFixed(2));
});
        
      } else {
          toCurrency.forEach((val, index) => {
    this.result.splice(index, 0, ((value / this.rates[fromCurrency]) * this.rates[toCurrency[index]]).toFixed(2));
});
          
      }
    },
    async fetchCurrencies() {
      const res = await fetch(
        "https://openexchangerates.org/api/currencies.json"
      );
      const data = await res.json();
      return data;
    },

    async fetchRates() {
      const res = await fetch(
        `http://api.exchangeratesapi.io/v1/latest?access_key=fa0563698916aceb386b4f8a28a6657c`
      );
      const { rates } = await res.json();
      this.rates = rates;
    },
  },
  async created() {
    const curr = await this.fetchCurrencies();
    this.currencies = Object.keys(curr).map((key) => [String(key), curr[key]]);
    await this.fetchRates();
  },
};
</script>