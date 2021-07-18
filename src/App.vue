<template>
  <v-app>
    <v-app-bar
      app
      color="purple"
    >
      <div class="d-flex align-center">
        <v-icon
        transition="scale-transition"
        size="40"
        color="#ffffff"
        >mdi-virus</v-icon>
      </div>
      <div class="ml-2 text-h6 white--text text-uppercase">
          Covid-19 Statistics
      </div>
    </v-app-bar>
    <v-row class="mt-15">
      <v-col cols="10" offset="1">
        <Table
          v-bind:arrCountries="arrCountries"
        >
        </Table>
      </v-col>
    </v-row>
    <h2 class="text-center mt-2 mb-4">Covid Dynamics</h2>
    <v-container>
      <v-row>
        <v-col cols="1" offset="3">
          <v-btn @click="dateDialog=true">
            <v-icon color="purple">mdi-calendar-text</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="3">
          <v-text-field 
            label="start" outlined rounded dense readonly
            :value="chartDates[0]"
            @click="dateDialog = true"
            :rules="dateRulse"
          />
        </v-col>
        <v-col cols="3">
          <v-text-field 
            label="end" outlined rounded dense readonly
            :value="chartDates[1]"
            @click="dateDialog = true"
            :rules="dateRulse"
          />
        </v-col>
      </v-row>
    </v-container>
    <v-container v-if="arrConfirmed.length > 0">
      <v-row>    
        <v-col cols="6" md="6" sm="12">
          <LineChart 
          label_1="Total Confirmed Covid Cases"
          label_2="Total Recovered"
          :chartData_1="confirmedTimeline" 
          :chartData_2="recoveredTimeline"
          :chartSettings_1="chartSettings_1" 
          :chartSettings_2="chartSettings_2" 
          :option="chartOptions" 
        ></LineChart>
        </v-col>
        <v-col cols="6" md="6" sm="12">
          <LineChart 
            label_1="New Confirmed Covid Cases"
            label_2="New Deathes"
            :chartData_1="newConfirmedTimeline" 
            :chartData_2="newDeathsTimeline"
            :chartSettings_1="chartSettings_1" 
            :chartSettings_2="chartSettings_2" 
            :option="chartOptions" 
          ></LineChart>
        </v-col>
      </v-row>
    </v-container>
     <v-dialog v-model="dateDialog" width="500">
        <v-card class="d-flex flex-column">
          <v-date-picker
            v-model="chartDates"
            range
          ></v-date-picker>
          <v-btn class="align-self-center mb-1" width="200" @click="dateDialog = false" rounded color="primary">
            OK
          </v-btn>
          <v-btn class="align-self-center mb-1" width="200" @click="dateDialog = false; chartDates = []" rounded color="primary">
            Cancel
          </v-btn>
        </v-card>
      </v-dialog>
      <h2 class="text-center mt-2 mb-2">{{countriesLidersCount}} Countries - Covid Leaders</h2>
      <v-container>
        <v-row>
          <v-col cols="1" offset="4">
            <v-btn height="48px" 
                  @click="countriesLidersCount > 1 ? countriesLidersCount-- : countriesLidersCount"
            ><v-icon>mdi-minus</v-icon></v-btn>
          </v-col>
          <v-col cols="2">
            <v-text-field 
            :label="String(countriesLidersCount)" 
            solo
            v-model="countriesLidersCount"
            :rules="numberRule"
          ></v-text-field>
          </v-col>
          <v-col cols="1">
          <v-btn height="48px" 
            @click="countriesLidersCount < 11 ? countriesLidersCount++ : countriesLidersCount"
          ><v-icon>mdi-plus</v-icon></v-btn>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="4" offset="4">
              <Doughnut
                v-if="arrCountries.length > 0"
                label="Donut"
                :chartData="arrCountries"
                :colorData="arrColors"
                :countriesCount="countriesLidersCount"
              ></Doughnut>
          </v-col>
        </v-row>        
      
      
      </v-container>
  </v-app>
</template>

<script>
import axios from 'axios';
import Table from '@/components/Table'
import LineChart from '@/components/LineChart.vue';
import Doughnut from '@/components/Doughnut.vue';
import randomColor  from 'randomcolor'

export default {
  name: 'App',

  components: {
    Table, LineChart, Doughnut
  },

  data: () => ({
    //arrays
    arrConfirmed: [],
    arrNewConfirmed: [],
    arrRecovered: [],
    arrNewRecovered: [],
    arrDeaths: [],
    arrNewDeaths: [],
    arrCountries: [],
    arrColors: [],
    //chart
    chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      },
      chartSettings_1: {
        fill: false,
        pointRadius: 0,
        pointHoverRadius: 6,
        borderColor: randomColor()
      },
      chartSettings_2: {
        fill: false,
        pointRadius: 0,
        pointHoverRadius: 6,
        borderColor: randomColor()
      },
      chartDates: [],
      dateDialog: false,
      dateRulse: [v => !!v || 'Date is required'],
      numberRule: [v => Number.isNaN(v) || Number(v) || 'Number reqired'],
      countriesLidersCount: 5
  }),
  methods: {
    async dataLoad(){
      //timeline datas
      const {data} = await axios.get("https://corona-api.com/timeline");

      data.data.forEach(el => {
        this.arrConfirmed.push({date: el.date, count: el.confirmed})
        this.arrNewConfirmed.push({date: el.date, count: el.new_confirmed})
        this.arrRecovered.push({date: el.date, count: el.recovered})
        this.arrNewRecovered.push({date: el.date, count: el.new_recovered})
        this.arrDeaths.push({date: el.date, count: el.deaths})
        this.arrNewDeaths.push({date: el.date, count: el.new_deaths})
      });

      this.chartDates.push(this.arrConfirmed[this.arrConfirmed.length - 1].date);
      this.chartDates.push(this.arrConfirmed[0].date);
      
      //countries
      const countriesData = await axios.get("https://corona-api.com/countries");
      countriesData.data.data.forEach(el => {
        this.arrCountries.push({
          name: el.name,
          population: el.population,
          total_confirmed: el.latest_data.confirmed,
          total_recovered: el.latest_data.recovered,
          total_deaths: el.latest_data.deaths,
          today_confirmed: el.today.confirmed,
          today_deaths: el.today.deaths,
        })
        this.arrColors.push(randomColor())
      });
    }
  },
  mounted() {
     this.dataLoad();
  },
  computed: {
    confirmedTimeline: function() {
      let array = [];
      let startDate = new Date(this.chartDates[0]);
      let endDate = new Date(this.chartDates[1]);

      this.arrConfirmed.forEach(el => {
        let currentDate = new Date(el.date);
        if (currentDate < endDate && currentDate > startDate){
          array.push(el);
        }
      })
      return array;
    },
    recoveredTimeline: function() {
      let array = [];
      let startDate = new Date(this.chartDates[0]);
      let endDate = new Date(this.chartDates[1]);

      this.arrRecovered.forEach(el => {
        let currentDate = new Date(el.date);
        if (currentDate < endDate && currentDate > startDate){
          array.push(el);
        }
      })
      return array;
    },
    newConfirmedTimeline: function() {
      let array = [];
      let startDate = new Date(this.chartDates[0]);
      let endDate = new Date(this.chartDates[1]);

      this.arrNewConfirmed.forEach(el => {
        let currentDate = new Date(el.date);
        if (currentDate < endDate && currentDate > startDate){
          array.push(el);
        }
      })
      return array;
    },
    newDeathsTimeline: function() {
      let array = [];
      let startDate = new Date(this.chartDates[0]);
      let endDate = new Date(this.chartDates[1]);

      this.arrNewDeaths.forEach(el => {
        let currentDate = new Date(el.date);
        if (currentDate < endDate && currentDate > startDate){
          array.push(el);
        }
      })
      return array;
    }
  }
};
</script>
