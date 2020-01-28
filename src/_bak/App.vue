<template>
  <div id="app">
    <Navbar/>
    <b-container fluid="md" class="main-container border shadow-sm p-3 mt-5 bg-white rounded">
      <b-row align-v="center" align-h="center">
        <Factor 
          v-for="factor in factors.datasets"
          v-bind:key="factor.id"
          v-bind:factor="factor"
          v-bind:label.sync="factor.label"
          v-bind:data.sync="factor.data"
        ></Factor>
      </b-row>
      <b-row cols="1" class="graph-container mt-3 p-3">
        <b-col id="chart" class="rounded">
          <Chart :chartdata="factors" :options="chartOptions"/>
        </b-col>
      </b-row>
      <b-row cols="1">
         <b-col 
          v-for="factor in factors.datasets"
        >{{factor.label + ' -> ' + factor.data}}</b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Navbar from './components/Navbar.vue'
import Factor from './components/Factor.vue'
import Chart from './components/Chart.vue'

export default {
  name: 'app',
  components: {
    Navbar,
    Factor,
    Chart
  },
  data () {
    return {
      factors: {
        labels: Array.from(Array(30+1).keys()),
        datasets: [
          {
            id: 1,
            label: 'Настроение',
            data: [1,3,0,1,2,4,5,5,5,5] 
          },
          {
            id: 2,
            label: 'Энергия',
            data: [1,3,0,1,2,4,5] 
          },
          {
            id: 3,
            label: 'Активность',
            data: [1,3,0,1,2,4,5] 
          },
          {
            id: 4,
            label: 'Активность',
            data: [1,3,0,1,2,4,5] 
          },
          {
            id: 5,
            label: 'Активность',
            data: [1,3,0,1,2,4,5] 
          },
          {
            id: 6,
            label: 'Активность',
            data: [1,3,0,1,2,4,5] 
          },
        ],
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      },
      completedToday: false,
      showMonth: false
    }
  },
  watch: {
    'factors.datasets': {
      handler: "saveFactors",
      deep: true,
    }
  },
  mounted() {
    // localStorage.removeItem('factors');
    if (localStorage.getItem('factors')) {
      try {
        this.factors.datasets = JSON.parse(localStorage.getItem('factors'));
      } catch(e) {
        localStorage.removeItem('factors');
      }
    }
  },
  methods: {
    addFactor: function (factor) {
      this.factors.datasets.push(factor);
      this.saveFactors();
    },
    saveFactors() {
      console.log(this.factors.datasets);
      const parsed = JSON.stringify(this.factors.datasets);
      localStorage.setItem('factors', parsed);
    }
  },
}
</script>
