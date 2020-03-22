<template>
  <v-app>
    <div class="bunner">
      <v-container>
        <h1 class="bunner__title">Covid 19 global data</h1>
        <info-cards
          :info-data="globalData"
        />
      </v-container>
    </div>

    <div class="main-info">
      <v-container>
        <v-row justify="center">
          <h2>Top 10 Confirmed Countries</h2>
        </v-row>
        <v-row justify="center">
          <v-col md="6">
            <horizontal-bar-chart
              v-if="loaded"
              :chart-data="topChartData"
            />
          </v-col>
        </v-row>
      </v-container>

      <v-container>
        <v-row justify="center">
          <h2>Global Confirmed/Recoveres </h2>
        </v-row>
        <v-row
          justify="center"
          class="mb-6"
        >
          <v-col lg="8">
            <line-chart v-if="loaded" :chart-data="chartData"></line-chart>
          </v-col>
        </v-row>
      </v-container>

      <v-container>
        <v-row justify="center">
            <h2>Location Information</h2>
        </v-row>
        <v-row justify="center">
          <h2 v-if="localData.country">
            {{ localData.country }} - {{ countries[localData.country] }}
          </h2>
        </v-row>
        <v-row justify="center">
          <v-col lg="5">
            <v-select
              v-model="localData.country"
              :items="countriesList"
              item-text="name"
              label="Select countries"
              @change="getCountryData(countries[localData.country])"
            ></v-select>
          </v-col>
        </v-row>

        <info-cards
            :info-data="localData"
          />
      </v-container>
    </div>
  </v-app>
</template>

<script>
import axios from 'axios';
import InfoCards from './components/InfoCards.vue';
import LineChart from './components/LineChart.vue';
import HorizontalBarChart from './components/HorizontalBarChart.vue';
import './styles/styles.scss';

const baseURL = 'https://covid19.mathdro.id/api';

export default {
  name: 'App',
  components: {
    InfoCards,
    LineChart,
    HorizontalBarChart,
  },
  data() {
    return {
      globalData: {
        confirmed: 0,
        recovered: 0,
        deaths: 0,
      },
      localData: {
        country: '',
        confirmed: 0,
        recovered: 0,
        deaths: 0,
      },
      countries: {},
      countriesList: [],
      countriesCode: [],
      items: ['Foo', 'Bar', 'Fizz', 'Buzz'],
      datacollection: null,
      dailyData: [],
      dailyDataConfirmed: [],
      dailyDataRecovered: [],
      dailyDataDeaths: [],
      dailyArrayData: {
        totalConfirmed: [],
        totalRecovered: [],
        mainlandChina: [],
        otherLocations: [],
        reportDateString: [],
        deltaConfirmed: [],
        deltaRecovered: [],
      },
      chartData: {
        labels: [],
        datasets: [],
      },
      topChartData: {
        labels: [],
        datasets: [
          {
            label: 'Confirmed',
            data: [],
            backgroundColor: 'rgba(95, 158, 160, 1)',
            barPercentage: 0.5,
          },
        ],
      },
      loaded: true,
    };
  },
  methods: {
    getGlobalData() {
      axios.get(baseURL)
        .then((resp) => {
          this.globalData.confirmed = resp.data.confirmed.value;
          this.globalData.recovered = resp.data.recovered.value;
          this.globalData.deaths = resp.data.deaths.value;
        })
        .catch((err) => console.error(err));
    },
    getTopConfirmedCountries() {
      axios.get(`${baseURL}/confirmed`)
        .then((resp) => {
          resp.data.forEach((el, i) => {
            if (i < 10) {
              this.topChartData.labels[i] = resp.data[i].countryRegion;
              this.topChartData.datasets[0].data[i] = resp.data[i].confirmed;
            }
          });
        })
        .catch((err) => console.log(err));
    },
    getGlobalDailyData() {
      this.loaded = false;
      axios.get(`${baseURL}/daily/`)
        .then((resp) => {
          this.dailyData = resp.data;
          this.chartDataFormation();
          this.loaded = true;
        })
        .catch((err) => console.log(err));
    },
    chartDataFormation() {
      this.dailyData.forEach((el, i) => {
        this.dailyArrayData.totalConfirmed[i] = el.totalConfirmed;
        this.dailyArrayData.totalRecovered[i] = el.totalRecovered;
        this.dailyArrayData.mainlandChina[i] = el.mainlandChina;
        this.dailyArrayData.otherLocations[i] = el.otherLocations;
        this.dailyArrayData.reportDateString[i] = el.reportDateString;
        this.dailyArrayData.deltaConfirmed[i] = el.deltaConfirmed;
        this.dailyArrayData.deltaRecovered[i] = el.deltaRecovered;
        this.dailyArrayData.reportDateString[i] = el.reportDateString;
      });

      this.chartData.labels = this.dailyArrayData.reportDateString;
      this.chartData.datasets = [
        {
          label: 'Global Confirmed',
          data: this.dailyArrayData.totalConfirmed,
          backgroundColor: 'rgba(50, 50, 50, 0.1)',
          borderColor: 'rgba(50, 50, 50, .5)',
          // yAxisID: 'y-axis-global-confirmed',
        },
        {
          label: 'Global Recovered',
          data: this.dailyArrayData.totalRecovered,
          backgroundColor: 'rgba(0, 128, 0, 0.6)',
          borderColor: 'rgba(0, 128, 0, 1)',
          // yAxisID: 'y-axis-global-recovered',
        },
        {
          label: 'China Confirmed',
          data: this.dailyArrayData.mainlandChina,
          backgroundColor: 'rgba(255, 177, 0, 0.6)',
          borderColor: 'rgba(255, 177, 0, 1)',
        },
      ];
    },
    getCountryData(countryID) {
      axios.get(`${baseURL}/countries/${countryID}`)
        .then((resp) => {
          this.localData.confirmed = resp.data.confirmed.value;
          this.localData.recovered = resp.data.recovered.value;
          this.localData.deaths = resp.data.deaths.value;
        })
        .catch((err) => console.log(err));
    },
    getCountriesList() {
      axios.get(`${baseURL}/countries`)
        .then((resp) => {
          this.countries = resp.data.countries;
          this.countriesList = Object.keys(this.countries);
          this.countriesCode = Object.values(this.countries);
        })
        .catch((err) => console.log(err));
    },
    getRandomInt() {
      return Math.floor(Math.random() * (50 - 5 + 1)) + 5;
    },
    fillData() {
      this.datacollection = {
        labels: [this.getRandomInt(), this.getRandomInt()],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: '#f87979',
            data: [this.getRandomInt(), this.getRandomInt()],
          }, {
            label: 'Data One',
            backgroundColor: '#f87979',
            data: [this.getRandomInt(), this.getRandomInt()],
          },
        ],
      };
    },
  },
  mounted() {
    this.getGlobalData();
    this.getTopConfirmedCountries();
    this.getCountriesList();
    this.getGlobalDailyData();
    this.fillData();
  },
};
</script>

<style lang="scss">
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
} */
.main-info {
  position: relative;
  z-index: 1;

  &:before {
    content: '';
    z-index: -1;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    position: absolute;
    background: url(/images/covid-19_2.webp) center center;
    background-size: cover;
    opacity: .2;
  }
}
</style>
