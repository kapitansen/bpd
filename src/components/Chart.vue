<script>
	import { Line } from 'vue-chartjs'

	const addSubtractDate = require("add-subtract-date");
	const datesBetween = require('dates-between');

	let colors = [
		"rgb(255, 99, 132)",   // red 
		"rgb(255, 159, 64)",   // orange
		"rgb(255, 45, 55)",    // red
		"rgb(75, 192, 192)",   // green
		"rgb(54, 162, 235)",   // blue
		"rgb(153, 102, 255)",  // purple
		"rgb(255, 205, 86)",   // yellow
		"rgb(201, 203, 207)",  // grey
	];

	export default {
		extends: Line,
		props: {
			factorsdata: Array,
			showMonth: Boolean,
			options: Object,
		},
		computed: {
			chartdata: function () {
				let fdata = JSON.parse(JSON.stringify(this.factorsdata));
				let chartlabels = [];
				let chartdatasets = [];
				// this.showMonth == true? chartlabels = Array.from(Array(30+1).keys()) : chartlabels = Array.from(Array(14+1).keys());
				// chartlabels.shift();
				
				if (fdata.length > 0) {
					// Находим самый длинный массив данных
					let lengths = fdata.map(function(f){return f.data.length;});
					var data_max = (Math.max.apply(Math, lengths));

					// new
					let graphDays = 13;
					if (this.showMonth == true) graphDays = 29;
					if (data_max < graphDays+1) {
						let startDate1 = addSubtractDate.subtract(new Date(), data_max-1, "day");
						let endDate1 = addSubtractDate.add(new Date(startDate1.getTime()), graphDays, "day");
						for (const date of datesBetween(startDate1, endDate1)) {
							let month = date.getMonth() + 1;
						    chartlabels.push(date.getDate() + '/' + month);
						}
					}
					if (data_max >= graphDays+1) { 
						let startDate2 = addSubtractDate.subtract(new Date(), graphDays-1, "day");
						let endDate2 = new Date();
						for (const date of datesBetween(startDate2, endDate2)) {
							let month = date.getMonth() + 1;
						    chartlabels.push(date.getDate() + '/' + month);
						}
					}			
					// end new

					chartdatasets = fdata.map((factor, i) => {
						if (this.showMonth != true) {
							if (data_max > 14) {
								factor.data = factor.data.slice(data_max-14);
							}
						}
						factor.borderColor = colors[i];
						factor.backgroundColor = colors[i]; 
						factor.fill = false; 
						factor.tension = 0.2; 
						return factor;
					});
				}
				return {
					labels: chartlabels,
					datasets: chartdatasets
				}
			}
		},
		watch: {
		  chartdata: {
		    handler: "render",
		    deep: true,
		  },
		  showMonth: {
		    handler: "render",
		  },
		},
		mounted () {
			this.render();
		},
		methods: {
			render: function () {
				this.renderChart(this.chartdata, this.options);
			}
		}
	}
</script>
