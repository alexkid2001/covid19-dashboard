<script>
import { Line } from 'vue-chartjs';

export default {
  extends: Line,
  props: ['chartData'],
  data() {
    return {
      chartOptions: {
        legend: {
          position: 'bottom',
        },
        scales: {
          xAxes: [{
            // barPercentage: 1,
            // categoryPercentage: 0.6,
            // gridLines: {
            //   display: false,
            // },
            ticks: {
              maxTicksLimit: 8,
            },
          }],
          // yAxes: [{
          //   id: 'y-axis-global-confirmed',
          // }, {
          //   id: 'y-axis-global-recovered',
          // }],
          // yAxes: [{
          //   stacked: true,
          //   scaleLabel: {
          //     display: true,
          //   },
          // }],
        },
      },
    };
  },
  mounted() {
    this.renderChart(this.chartData, this.chartOptions);
  },
};
</script>
