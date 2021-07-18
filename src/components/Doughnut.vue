<script>
import { Doughnut } from 'vue-chartjs' 
//import func from '../../vue-temp/vue-editor-bridge';

export default ({
    extends: Doughnut,
    props: {
        label: {
            type: String
        },
        chartData: {
            type: Array
        },
        colorData: {
            type: Array
        },
        option: {
            type: Object
        },
        countriesCount: {
            type: Number
        }
    },
    mounted () {
     this.render();   
    },
    methods: {
        render(){
            let total = 0;
        
            this.chartData.forEach(element => {
                total += element.total_confirmed;
            });
            
            const countriesName = [];
            const newConfirmed = [];
            const sortedData = this.chartData.sort((a,b) => 
                    b.total_confirmed - a.total_confirmed);
            let summ = 0;
            for (let index = 0; index < this.countriesCount; index++) {
                countriesName.push(sortedData[index].name);
                newConfirmed.push(sortedData[index].total_confirmed);
                summ += sortedData[index].total_confirmed;
            }
            countriesName.push('Others');
            newConfirmed.push(total - summ);
            
            this.renderChart({
                labels: countriesName,
                datasets: [
                    {
                    label: this.label,
                    data: newConfirmed,
                    backgroundColor: this.colorData,
                    hoverOffset: 4
                    }
                ]
            }, this.option
            )
        }
    },
    watch: {
        countriesCount: function(){
            this.render();
        }
    }
})
</script>
