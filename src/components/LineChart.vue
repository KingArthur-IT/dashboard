<script>
import { Line } from 'vue-chartjs' 

export default ({
    extends: Line,
    props: {
        label_1: {
            type: String
        },
        chartData_1: {
            type: Array
        },
        label_2: {
            type: String
        },
        chartData_2: {
            type: Array
        },
        chartSettings_1: {
            type: Object
        },
        chartSettings_2: {
            type: Object
        },
        option: {
            type: Object
        }
    },
    mounted () {
        const dates = this.chartData_1.map(d => d.date).reverse();
        const totals_1 = this.chartData_1.map(d => d.count).reverse();
        const totals_2 = this.chartData_2.map(d => d.count).reverse();
        
        this.renderChart({
            labels: dates,
            datasets: [
                {
                label: this.label_1,
                data: totals_1,
                fill:  this.chartSettings_1.fill,
                pointRadius: this.chartSettings_1.pointRadius,
                pointHoverRadius: this.chartSettings_1.pointHoverRadius,
                borderColor: this.chartSettings_1.borderColor,
                },
                {
                label: this.label_2,
                data: totals_2,
                fill:  this.chartSettings_2.fill,
                pointRadius: this.chartSettings_2.pointRadius,
                pointHoverRadius: this.chartSettings_2.pointHoverRadius,
                borderColor: this.chartSettings_2.borderColor,
                },
            ]
        }, this.option
        )
    }
})
</script>
