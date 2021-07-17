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
      <v-col>
        <Table
          v-bind:arrCountries="arrCountries"
        >
        </Table>
      </v-col>
    </v-row>
    <div v-if="arrConfirmed.length > 0">
      <h2 class="text-center mt-2 mb-2">Covid Dynamics</h2>
      <div class="d-flex justify-center relative">    
        <LineChart 
          width="600px"
          label_1="Confirmed Covid Cases"
          label_2="Recovered"
          :chartData_1="arrConfirmed" 
          :chartData_2="arrRecovered"
          :chartSettings_1="chartSettings_1" 
          :chartSettings_2="chartSettings_2" 
          :option="chartOptions" 
        ></LineChart>
        <LineChart 
          width="600px"
          label_1="New Confirmed Covid Cases"
          label_2="New Deathes"
          :chartData_1="arrNewConfirmed" 
          :chartData_2="arrNewDeaths"
          :chartSettings_1="chartSettings_1" 
          :chartSettings_2="chartSettings_2" 
          :option="chartOptions" 
        ></LineChart>
      </div>
      <h2 class="text-center mt-2 mb-2">Leaders of Covid</h2>
      <v-container class="d-flex justify-center">
        <v-row class="d-flex">
          <v-col cols="4" offset="2">
              <Doughnut
                v-if="arrCountries.length > 0"
                label="Donut"
                :chartData="arrCountries"
                :colorData="arrColors"
              ></Doughnut>
          </v-col>
          <v-col cols="1" offset="1">
            <v-btn><v-icon>mdi-minus</v-icon></v-btn>
          </v-col>
          <v-col cols="1">
            <v-text-field label="5" solo></v-text-field>
          </v-col>
          <v-col cols="1">
          <v-btn><v-icon>mdi-plus</v-icon></v-btn>
          </v-col>
        </v-row>        
      
      
      </v-container>
    </div>
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
      }
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
  }
};
</script>
