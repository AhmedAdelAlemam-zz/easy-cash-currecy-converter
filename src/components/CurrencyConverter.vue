<template>
  <b-container>
    <b-row>
      <b-col cols="2" />
      <b-col cols="8">
        <b-card class="rounded mb-3">
          <h1>Currency converter app</h1>
        </b-card>
      </b-col>
    </b-row>
    <b-row>
      <b-col cols="2" />
      <b-col cols="6">
        <b-form>
          <label class="float-left pl-2 h4">From</label>
          <b-form-group id="input-group-1" label-for="input-1">
            <input
              class="form-control"
              id="fromInput"
              v-model="inputed"
              type="number"
              min="1"
              required
            />
          </b-form-group>
        </b-form>
        <span
          class="text-danger float-left mb-2"
          v-if="(isconvertClicked && !inputed) || inputed < 0"
          >{{ errorMsg }}</span
        >
      </b-col>
      <b-col cols="2" class="mt-4 pt-1">
        <b-select
          class="mt-2"
          v-model="selected[0]"
          :options="countries"
          @change="getFromValueOnSelect"
        >
        </b-select>
      </b-col>
    </b-row>
    <b-row>
      <b-col cols="2" />
      <b-col cols="6">
        <b-form>
          <label class="float-left pl-2 h4">To</label>
          <b-form-group id="input-group-1" label-for="input-1">
            <b-form-input type="text" readonly v-model="result"></b-form-input>
          </b-form-group>
        </b-form>
      </b-col>
      <b-col cols="2" class="mt-4 pt-1">
        <b-select
          class="mt-2"
          :options="countries"
          v-model="selected[1]"
          @change="getToValueOnSelect"
        >
        </b-select>
      </b-col>
    </b-row>
    <b-row class="mt-3">
      <b-col cols="4" />
      <b-col cols="4">
        <b-button type="button" variant="outline-dark" @click="convert">
          Convert
        </b-button>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import Axios from "axios";
export default {
  name: "App",
  data() {
    return {
      selected: ["EGP", "EUR"],
      inputed: "1",
      result: null,
      countries: [],
      rates: {},
      fromCurrency: "EGP",
      toCurrency: "EUR",
      errorMsg: "",
      isconvertClicked: false,
    };
  },
  methods: {
    convertedRate() {
      this.result = this.inputed * this.rates[this.toCurrency];
      if (this.fromCurrency !== this.toCurrency) {
        this.result = this.result.toFixed(3);
      }
    },
    convert() {
      if (this.inputed < 0) {
        this.errorMsg = "Please enter a valid amount to convert";
        this.result = "";
        return false;
      } else if (this.inputed) {
        this.convertedRate();
        this.isconvertClicked = false;
      } else {
        this.isconvertClicked = true;
        this.result = "";
        this.errorMsg = "Please enter an amount to convert";
      }
    },
    getFromValueOnSelect(value) {
      this.fromCurrency = value;
      Axios.get(
        `https://v6.exchangerate-api.com/v6/d7fa812453b4d48a266e0e82/latest/${value}`
      ).then((response) => {
        this.rates = response.data.conversion_rates;
      });
    },
    getToValueOnSelect(value) {
      this.toCurrency = value;
    },
  },
  mounted() {
    Axios.get(
      `https://v6.exchangerate-api.com/v6/d7fa812453b4d48a266e0e82/latest/${this.fromCurrency}`
    ).then((response) => {
      this.rates = response.data.conversion_rates;
      for (let rate in this.rates) {
        this.countries.push(rate);
      }
      this.convertedRate();
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
