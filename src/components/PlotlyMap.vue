<template>
    <Plotly :data="chartData" :layout="layoutContour" :display-mode-bar="false"></Plotly> 
</template>

<script>
import { Plotly } from 'vue-plotly'
import country from 'country-iso-2-to-3'

export default {
    components: {
        Plotly
    },
    props: {
        codes: {
            type: Array
        }
    },
    data () {
        return {
            layoutContour : {
                showlegend: false,
                autosize: false,
                width: 600,
                height: 550,
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
            
        }
    },
    methods: {
        
    },
    mounted () {
        
    }, 
    computed: {
        chartData() {
            let iso3codes = [];
            let sizes = [];
            let colors = [];
            this.codes.forEach(element => {
                iso3codes.push(country(element));
                sizes.push(30);
                colors.push(Math.random()* 30);
            });
            let obj = [{
                type: 'scattergeo',
                mode: 'markers',
                locations: iso3codes,
                marker: {
                    size: sizes,
                    color: colors,                    
                },
                name: 'europe data'
            }]
            return obj;
        }
    }
}
</script>