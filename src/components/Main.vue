<template>
  <v-container>
    <h1 class="display-3 font-weight-bold mb-3">
      The Road to Ten Gigatons
    </h1>
    <h2 class="font-weight-regular display-1 mb-16">Carbon Removal Scale Up Challenge</h2>

<v-row>

<v-col cols="3" style="background-color: #777">
<div>
  <span class="big">{{pprint('scale', tonsSequestered).big}}</span>
  <span class="little">{{pprint('scale', tonsSequestered).little}}</span>
</div>
<div class="caps-label">SCALE</div>
</v-col>

<v-col cols="1"></v-col>

<v-col cols="6" >
<div>
Your solution has <span class="bright" style="font-size: 1em;">medium permanence</span>
<div style="display:grid; grid-template-columns: 1fr 1fr 1fr">  
  <div v-for="perm in permanences" 
      :key="'val'+perm.name">
    <span class="text-h4">{{pprint('scale', perm.value).big}}</span> <span class="little">{{pprint('scale', perm.value).little}}</span>
  </div>

  <div v-for="perm in permanences" :key="'name'+perm.name">{{perm.name}}</div>
</div>
</div>
</v-col>

</v-row> <!-- end top row -->



<section class="mt-10" style="display:grid; grid-template-columns: 1fr 1fr 1fr;">

<div v-for="dim in dimensions" :key="dim">
  <span class="big">{{pprint(dim, estimates[dim]).big}}</span>
  <span class="little">{{pprint(dim, estimates[dim]).little}}</span>
</div> 

<div class="caps-label" v-for="dim in dimensions" :key="'label'+dim">{{dim}}</div>
</section>


<h3 class="mt-10" style="display:inline-block">Solutions</h3>
<p class="bright" style="display:inline-block; margin-left: 16px;">Move the sliders until you achieve 10 Gigaton scale.</p>

<section style="display:grid; grid-template-columns: 1fr 1fr; grid-column-gap: 10%; grid-row-gap: 24px;">

<!-- https://vuetifyjs.com/en/api/v-slider/#slots -->
<v-slider
  v-model="tonsAllocated[solution]"
  v-for="solution in solutions"
  :key="solution"
  :hint="'$' + costPerTonEstimate(solution) + '/ton'"
  :max="maxAlloc"
  :min="minAlloc"
  :thumb-size="16"
  thumb-label="always"
  style="min-width: 200px"
  persistent-hint
>
  
<span slot="label" class="solution-label">{{pprintSolution(solution)}}</span>

<span slot="thumb-label">{{pprint('scale', tonsAllocated[solution]).big}}T</span>

</v-slider>
</section>


<v-row class="mt-10">
<v-col>
<p class="bright">How is the captured CO2 used?</p>
<v-slider
  v-model="percentUtilization"
  :min="0"
  :max="100"
  :thumb-size="16"
  thumb-label="always"
  persistent-hint
></v-slider>

<div style="position:relative; bottom:12px; color: #777; font-size:0.8em; text-align: center">{{percentUtilization}}% utilized, {{100-percentUtilization}}% sequestered</div>
</v-col>

<v-col>
<p class="bright">Energy sources for DAC</p>

<v-btn-toggle
  v-model="dacEnergySource"
  shaped
  mandatory
>
  <v-btn value="natural gas">
    Nat Gas
  </v-btn>
  <v-btn value="geothermal">
    Geothermal
  </v-btn>
  <v-btn value="nuclear">
    Nuclear
  </v-btn>
  <v-btn value="csp">
    CSP
  </v-btn>
</v-btn-toggle>
</v-col>
</v-row>


<h3 class="mt-5">Simulated news</h3>
<div class="news" v-for="newsItem in newsItems" :key="newsItem">{{newsItem}}</div>


<v-row >
  <v-col cols="12">
    <v-img
      :src="require('../assets/world.png')"
      class="my-3"
      contain
    />
  </v-col>

<!--       <v-col class="mb-4 col-md-8" >
        <h1 class="display-3 font-weight-bold my-3">
          The Road to Ten Gigatons
        </h1>

        <p class="subheading font-weight-regular">
          <strong>Play to save Earth</strong> by removing 10 Gigatons of CO2 with these solutions. Todo provide more context around why.
        </p>

        <v-btn
          class="ma-2 yellow darken-1 font-weight-black"
        >
          PLAY
        </v-btn>

        <h3>Direct Air Capture (DAC)</h3>

        <p>
          Strengths and weaknesses and open questions. John to fill in.
        </p>


        <h3>Forests</h3>

        <p>
          Strengths and weaknesses and open questions. John to fill in.
        </p>
      </v-col> -->



    </v-row>
  </v-container>
</template>

<script>

import _ from "lodash";

const TEN_BILLION = 10000000000;

  export default {
    name: 'Main',


    computed: {

      tonsSequestered(){
        return _.sum(_.values(this.tonsAllocated));
      },

      estimates(){
        // TODO(John): Return actual estimates.
        return {
          cost: this.tonsAllocated.forests + this.tonsAllocated.dac,
          land: this.tonsAllocated.soil + this.tonsAllocated.beccs,
          energy: 10 * (this.tonsAllocated.soil + this.tonsAllocated.forests)
        };
      },
      permanences(){
        // TODO(John): Return actual estimates.
        return [
          {name: '<50 years', value: this.tonsAllocated.soil + this.tonsAllocated.forests},
          {name: '50-200 years', value: this.tonsAllocated.beccs * 2},
          {name: '>200 years', value: this.tonsAllocated.dac},
        ];
      },

      newsItems(){
        // TODO(John): Add logic to return the right news items based on the values of the data variables.
        return [
          '2030: Food prices rising due to BECCS competition for land!',
          '2030: You’ve catalyzed a movement of farmers in the Corn Belt to move to regenerative agriculture!'
        ];
      }


    },
    data(){

      return {
        dimensions: ['cost', 'land', 'energy'],
        solutions: ['forests', 'dac', 'beccs', 'soil'],
        tonsAllocated: {
          forests: 0,
          dac: 0,
          beccs: 0,
          soil: 0,          
        },

        minAlloc: 0,
        maxAlloc: TEN_BILLION,

        dacEnergySource: 'csp',
        percentUtilization: 50,
      }
    },

    methods: {

      // Return the cost per ton (can be dynamic).
      costPerTonEstimate(sol){
        // TODO(John): Return actual estimates.
        return {
          forests: 10,
          beccs: 50,
          dac: 100,
          soil: 70
        }[sol];
      },

      // Pretty print solution string.
      pprintSolution(sol){
        return {
          forests: 'Forests',
          dac: 'DAC',
          beccs: 'BECCS',
          soil: 'Soil'
        }[sol];
      },

      // Pretty print big numbers.
      pprint(key, val){
        const littles = {
          scale: 'tons',
          cost: 'USD',
          land: 'ha',
          energy: 'EJ'
        };

        const prefix = key === 'cost' ? '$' : '';
        return {
          big: prefix + this.formatNumber(val),
          little: littles[key]
        };
      },

      // Helper function for formatting big numbers.
       formatNumber(value) {
        var limits = [
          { limit: 1e9, suffix: "B" },
          { limit: 1e6, suffix: "M" },
          { limit: 1e3, suffix: "k" },
        ];

        for (const lim of limits) {
          if (value >= lim.limit) {
            return (value / lim.limit).toFixed(1) + lim.suffix;
          }
        }
        return value.toString();
      },

    }
  }
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.bright {
  color: #ecc400;
  font-weight: bold;
  font-size: 0.8em;
}

.big{
  font-weight: bold;
  font-size: 3em;
}

.caps-label{
  font-weight:bold;
  opacity: 0.5;
  text-transform: uppercase;
}

.unit{
  margin-left: 4px;
  font-weight:bold;
  opacity: 0.5;
}

.solution-label{
  text-transform: capitalize;
  font-size: 12px;
}

.news{
  margin-top: 8px;
}

</style>