<script>
import { HorizontalBar } from 'vue-chartjs';

export default {
  extends: HorizontalBar,
  props: ['chartData', 'options'],
  data() {
    return {
      chartOptions: {
        legend: {
          position: 'bottom',
        },
        // scales: {
        //   yAxes: [{
        //     // barPercentage: 0.5,
        //   }],
        // },
        elements: {
          rectangle: {
            borderSkipped: 'left',
          },
        },
      },
    };
  },
  mounted() {
    this.renderChart(this.chartData, this.chartOptions);
  },
};
</script>
