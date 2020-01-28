<script>
	import { Line } from 'vue-chartjs'
	export default {
		extends: Line,
		props: {
			chartdata: {
				type: Object,
				default: null
			},
			options: {
				type: Object,
				default: null
			}
		},
		mounted () {
			this.renderChart(this.chartdata, this.options)
		}
	}
</script>

<style>
/*#chart {
  background: #f5f5f5;
  width: 100%;
  height: 150px;
}*/
</style>