<script>
	import { Line } from 'vue-chartjs'

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
				this.showMonth == true? chartlabels = Array.from(Array(30+1).keys()) : chartlabels = Array.from(Array(14+1).keys()),
				chartlabels.shift();
				chartdatasets = fdata.map((factor, i) => {
					factor.borderColor = colors[i];
					factor.backgroundColor = colors[i]; 
					factor.fill = false; 
					return factor;
				});
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
