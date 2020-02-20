<template>
  <div id="app">
    <b-navbar toggleable="sm" type="dark" variant="dark">
      <b-navbar-toggle target="nav-text-collapse"></b-navbar-toggle>

      <b-navbar-brand href="#">
        <img src="/favicon/favicon-32x32.png" class="d-inline-block align-top mr-1">
        Mood Journal
      </b-navbar-brand>

      <b-collapse id="nav-text-collapse" is-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          
          <b-nav-form>
              <b-form-radio-group
              v-model="showMonth"
              buttons
              name="radios-btn-default"
              size="sm"
              button-variant="outline-primary"
              >
                <b-form-radio v-model="showMonth" v-bind:value="false">Неделя</b-form-radio>
                <b-form-radio v-model="showMonth" v-bind:value="true">Месяц</b-form-radio>
              </b-form-radio-group>
          </b-nav-form>

          <b-nav-item-dropdown no-caret right>
            <template v-slot:button-content>
              <b-icon icon="list"/>
            </template>
            <b-dropdown-item @click="addFactor">Добавить фактор</b-dropdown-item>
            <b-dropdown-item @click="setRandomData(14)">14 случайных записей</b-dropdown-item>
            <b-dropdown-item @click="setRandomData(30)">30 случайных записей</b-dropdown-item>
            <b-dropdown-item @click="removeAllData">Удалить данные и начать с начала</b-dropdown-item>
            <b-dropdown-item @click="exportData">Показать данные для экспорта</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <b-container fluid="lg" class="main-container border shadow-sm p-3 mt-5 bg-white rounded">
      <b-row align-v="center" align-h="center" class="mt-3" cols="4">
        <Factor 
          v-for="factor in factors"
          v-bind:key="factor.id"
          v-bind:factor="factor"
          v-bind:label.sync="factor.label"
          v-bind:data.sync="factor.data"
          v-bind:descr.sync="factor.descr"
          v-on:delete="delFactor"
        ></Factor>
      </b-row>
      <b-row cols="1" class="graph-container mt-3 p-3">
        <b-col id="chart" class="rounded">
          <Chart :factorsdata="factors" :showMonth="showMonth" :options="chartOptions"/>
        </b-col>
      </b-row>
    </b-container>
    <b-modal id="importExportModal" title="Экспорт данных" centered :hide-footer="true">
      <div id="exportjson">{{JSON.stringify(this.factors)}}</div>
    </b-modal>
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
    	        min: -3,
    	        max: 3,
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
     	lastChangeDate: '',
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
        this.lastChangeDate = localStorage.getItem('lastChangeDate');
      } catch(e) {
        localStorage.removeItem('factors');
        localStorage.removeItem('lastChangeDate');
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
    },
    delFactor: function (id) {
      if (confirm('Удалить параметр и все данные?')) {
        let factorId = this.factors.findIndex(factor => factor.id === id);
        console.log(factorId);
        this.factors.splice(factorId, 1);
      }
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

      let currentMonth = new Date().getMonth() + 1;
      let currentDate = new Date().getDate();
      this.lastChangeDate = currentMonth + '-' + currentDate;
      localStorage.setItem('lastChangeDate', this.lastChangeDate);
    },
    setRandomData: function (num) {
      if (confirm('Продолжить? Старые записи будут удалены.')) {
        this.factors = [];
        let newdata = [];
        for (let i=1; i < 6; i++) {
          newdata.push({
            id: i,
            label: 'Параметр'+i,
            data: randomDataSet(num,-3,3),
            descr: 'Какое-то описание',
          });
        }
        this.factors = newdata;
      }
    },
    removeAllData: function () {
      if (confirm('Продолжить? Старые записи будут удалены.')) {
        let newdata = [];
        for (let i=1; i < 6; i++) {
          newdata.push({
            id: i,
            label: 'Параметр'+i,
            data: [],
            descr: 'Какое-то описание',
          });
        }
        this.factors = newdata;
      }
    },
    exportData: function () {
      this.$bvModal.show('importExportModal');
      // document.getElementById('exportjson').innerHTML = JSON.stringify(this.factors);
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
  div#exportjson {
      font-size: 10px;
      background: #fff5d8;
      padding: 8px;
  }
</style>