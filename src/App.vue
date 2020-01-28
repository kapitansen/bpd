<template>
  <div id="app">
    <b-navbar toggleable="sm" type="dark" variant="dark">
      <b-navbar-toggle target="nav-text-collapse"></b-navbar-toggle>

      <b-navbar-brand>BipolarBear</b-navbar-brand>

      <b-collapse id="nav-text-collapse" is-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">

          <b-nav-item-dropdown no-caret right>
            <template v-slot:button-content>
              <b-icon icon="list"/>
            </template>
            <b-dropdown-item @click="setRandomData(14)">14 случайных записей</b-dropdown-item>
            <b-dropdown-item @click="setRandomData(30)">30 случайных записей</b-dropdown-item>
            <b-dropdown-item @click="removeAllData">Удалить данные и начать с начала</b-dropdown-item>
            <b-dropdown-item @click="showMonth = !showMonth">Неделя/Месяц</b-dropdown-item>
            <b-dropdown-item @click="addFactor">Добавить фактор</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <b-container fluid="md" class="main-container border shadow-sm p-3 mt-5 bg-white rounded">
      <b-row align-v="center" align-h="center" class="mt-3">
        <Factor 
          v-for="factor in factors"
          v-bind:key="factor.id"
          v-bind:factor="factor"
          v-bind:label.sync="factor.label"
          v-bind:data.sync="factor.data"
        ></Factor>
      </b-row>
      <b-row cols="1" class="graph-container mt-3 p-3">
        <b-col id="chart" class="rounded">
          <Chart :factorsdata="factors" :showMonth="showMonth" :options="chartOptions"/>
        </b-col>
      </b-row>
      <!-- <b-row cols="1">
         <b-col 
          v-for="factor in factors"
        >{{factor.label + ' -> ' + factor.data}}</b-col>
      </b-row> -->
    </b-container>
  </div>
</template>

<script>
import Factor from './components/Factor.vue'
import Chart from './components/Chart.vue'

function randomDataSet(dataSetSize, minValue, maxValue) {
  return new Array(dataSetSize).fill(0).map(function(n) {
    return Math.round(Math.random() * (maxValue - minValue) + minValue);
  });
}

export default {
  name: 'app',
  components: {
    Factor,
    Chart
  },
  data () {
    return {
      factors: [],
      chartOptions: {
        responsive: true,
        legend: {
          position: 'bottom',
          labels: {
            usePointStyle: true,
          }
        },
        scales: {
          yAxes: [{
            ticks: {
              min: 1,
              max: 5,
              stepSize: 1
            }
          }]
        },
        maintainAspectRatio: false,
        tooltips: {
          mode: 'index',
          intersect: false,
        },
        hover: {
          mode: 'nearest',
          intersect: true
        },
      },
      completedToday: false,
      showMonth: false
    }
  },
  watch: {
    factors: {
      handler: "saveFactors",
      deep: true,
    }
  },
  mounted() {
    if (localStorage.getItem('factors')) {
      try {
        this.factors = JSON.parse(localStorage.getItem('factors'));
      } catch(e) {
        localStorage.removeItem('factors');
      }
    }
  },
  methods: {
    addFactor: function () {
      let newfactor = {
        "id": this.factors.length+1,
        "label": "Новый фактор",
        "data": []
      };
      this.factors.push(newfactor);
      this.saveFactors();
    },
    saveFactors() {
      let lengths = this.factors.map(function(f){return f.data.length;});
      var data_max = (Math.max.apply(Math, lengths));
      const parsed = JSON.stringify(this.factors);
      if (data_max > 30) {
        var newfactors = this.factors.map(function(f){
          f.data = f.data.slice(data_max-30);
          return f;
        });
        const parsed = JSON.stringify(this.newfactors);
      }
      localStorage.setItem('factors', parsed);
    },
    setRandomData: function (num) {
      this.factors = [];
      let newdata = [];
      for (let i=1; i < 6; i++) {
        newdata.push({
          id: i,
          label: 'Параметр'+i,
          data: randomDataSet(num,1,5) 
        });
      }
      this.factors = newdata;
    },
    removeAllData: function () {
      let newdata = [];
      for (let i=1; i < 6; i++) {
        newdata.push({
          id: i,
          label: 'Параметр'+i,
          data: [] 
        });
      }
      this.factors = newdata;
    }
  },
}
</script>

<style>
  body {
    background: url(/bg5.jpg);
    background-attachment: fixed;
    background-repeat: no-repeat;
    background-size: auto;
  }
  .bg-dark {
      background-color: #233140 !important;
  }
  .factor-edit {
    height: 23px;
  }
</style>