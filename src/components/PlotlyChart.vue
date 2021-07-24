<template>
    <Plotly :data="dataContour" :layout="layoutContour" :display-mode-bar="false"></Plotly> 
</template>

<script>
import { Plotly } from 'vue-plotly'

export default {
    components: {
        Plotly
    },
    data () {
        return {
            xContour : [],
            yContour : [],
            tContour : [],       
            layoutContour : {
                showlegend: false,
                autosize: false,
                width: 650,
                height: 600,
                margin: {t: 50},
                hovermode: 'closest',
                bargap: 0,
                xaxis: {
                    domain: [0, 0.85],
                    showgrid: false,
                    zeroline: false
                },
                yaxis: {
                    domain: [0, 0.85],
                    showgrid: false,
                    zeroline: false
                },
                xaxis2: {
                    domain: [0.85, 1],
                    showgrid: false,
                    zeroline: false
                },
                yaxis2: {
                    domain: [0.85, 1],
                    showgrid: false,
                    zeroline: false
                }
            },
            loading : false
        }
    },
    methods: {
        genNewData () {
            var N = 2000,
            a = -1.0,
            b = 1.2;

            var step = (b - a) / (N - 1);
            
            for(var i = 0; i < N; i++){
                this.tContour.push(a + step * i);
                this.xContour.push((Math.pow(this.tContour[i], 3)) + (0.3 * this.getNormal() ));
                this.yContour.push((Math.pow(this.tContour[i], 6)) + (0.3 * this.getNormal() ));
            }
        },
        getNormal () {
            var x = 0,
            y = 0,
            rds, c;
            do {
                x = Math.random() * 2 - 1;
                y = Math.random() * 2 - 1;
                rds = x * x + y * y;
            } while (rds == 0 || rds > 1);
            c = Math.sqrt(-2 * Math.log(rds) / rds); // Box-Muller transform
            return x * c * 2; // throw away extra sample y * c
        }
    },
    mounted () {
        this.genNewData();      
        
        this.loading = true;
    }, 
    computed: {
        dataContour () {
            let obj = [{
                x: this.xContour,
                y: this.yContour,
                mode: 'markers',
                name: 'points',
                marker: {
                    color: 'rgb(102,0,0)',
                    size: 2,
                    opacity: 0.4
                },
                type: 'scatter'
            },
            {
                x: this.xContour,
                y: this.yContour,
                name: 'density',
                ncontours: 40,
                colorscale: 'Hot',
                reversescale: true,
                showscale: true,
                type: 'histogram2dcontour',
                line: {
                    color: 'rgb(102,0,0)',
                    width: 0
                }
            },
            {
                x: this.xContour,
                name: 'x density',
                marker: {color: 'rgb(102,0,0)'},
                yaxis: 'y2',
                type: 'histogram'
            },
            {
                y: this.yContour,
                name: 'y density',
                marker: {color: 'rgb(102,0,0)'},
                xaxis: 'x2',
                type: 'histogram'
            }]
            return obj;
        }
    }
}
</script>