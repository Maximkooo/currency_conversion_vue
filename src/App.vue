<template>
  <div class="container w-50 pt-5 pb-5">
    <b-form @submit.prevent="onSubmit">
      <b-form-group label="Amount">
        <b-form-input
          v-model="form.amount"
          type="number"
          placeholder="Enter amount"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group label="From currency" class="mt-3">
        <b-form-select
          v-model="form.from_currency"
          :options="fromCurrencyOptions"
          :plain="true"
          required
        ></b-form-select>
      </b-form-group>

      <b-form-group label="To currency" class="mt-3">
        <b-form-select
          v-model="form.to_currency"
          :options="toCurrencyOptions"
          :plain="true"
          required
        ></b-form-select>
      </b-form-group>

      <div class="row mt-4">
        <div class="col-4">
          <b-button type="submit" variant="primary">Convert</b-button>
        </div>
        <div class="col-4 text-center">
          <h4 class="align-middle">
            Value: <b>{{ currentValue }}</b>
          </h4>
        </div>
        <div class="col-4 text-center">
          <h4>
            Total: <b>{{ totalActions }}</b>
          </h4>
        </div>
      </div>
    </b-form>

    <div v-if="history.length">
      <h1 class="text-center mt-5">History</h1>
      <History :items="history" @deleteItem="deleteItem" />
      <b-button
        variant="danger"
        class="text-center mx-auto d-block"
        @click="() => (history = [])"
      >
        Clear history
      </b-button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import History from "./components/History.vue";

export default {
  components: { History },
  data() {
    return {
      currentValue: 0,
      currencies: [],
      history: [],
      form: {},
      fromCurrencyOptions: [
        { text: "USD", value: { name: "USD", code: 840 } },
        { text: "EUR", value: { name: "EUR", code: 978 } },
        { text: "CHF", value: { name: "CHF", code: 756 } },
        { text: "GBP", value: { name: "GBP", code: 826 } },
        { text: "JOD", value: { name: "JOD", code: 400 } },
        { text: "KWD", value: { name: "KWD", code: 414 } },
        { text: "KZT", value: { name: "KZT", code: 398 } },
        { text: "LBP", value: { name: "LBP", code: 422 } },
        { text: "MAD", value: { name: "MAD", code: 504 } },
      ],
      toCurrencyOptions: [{ text: "UAH", value: { name: "UAH", code: 980 } }],
    };
  },
  created() {
    this.getData();
    this.interval = setInterval(() => {
      this.getData();
    }, 1000 * 60);
  },
  destroyed() {
    clearInterval(this.interval);
  },
  computed: {
    totalActions() {
      return this.history.length
        ? this.history
            .reduce((acc, item) => acc + Number(item.actions), 0)
            .toFixed(2)
        : 0;
    },
  },
  methods: {
    onSubmit() {
      const { amount, from_currency, to_currency } = this.form;

      const currency =
        this.currencies.find(
          (item) =>
            item.currencyCodeA === from_currency.code &&
            item.currencyCodeB === to_currency.code
        ) || {};

      if (Object.keys(currency).length) {
        this.limitHistory();
        this.currentValue = (
          amount * (currency.rateSell || currency.rateCross)
        ).toFixed(2);

        this.history.push({
          id: Math.floor(Math.random() * 10000),
          amount,
          from_currency,
          to_currency,
          actions: this.currentValue,
        });
      }
    },
    limitHistory() {
      if (this.history.length >= 20) {
        this.history = this.history.splice(1, this.history.length - 1);
      }
    },
    deleteItem(item) {
      this.history = this.history.filter((el) => item.id !== el.id);
    },
    getData() {
      axios
        .get(`https://api.monobank.ua/bank/currency`)
        .then((res) => (this.currencies = res.data));
    },
  },
};
</script>

