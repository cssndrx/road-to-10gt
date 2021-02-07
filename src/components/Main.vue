<template>
  <v-container>
    <h1 class="display-3 font-weight-bold mb-3">
      The Road to Ten Gigatons
    </h1>
    <h2 class="font-weight-regular display-1 mb-16">Carbon Removal Scale Up Challenge</h2>



<div>
  {{totalSequestered.value}}
  {{totalSequestered.unit}}
</div>

<div>
Your solution has <span class="bright">medium permanence</span>
<div style="display:grid; grid-template-columns: 1fr 1fr 1fr">  
  <div v-for="datum in totalPermanence" :key="'val'+datum.name">{{datum.value}} {{datum.unit}}</div>

  <div v-for="datum in totalPermanence" :key="'name'+datum.name">{{datum.name}}</div>
</div>
</div>



<section style="display:grid; grid-template-columns: 1fr 1fr 1fr">

<div>
  <span class="big">{{pprintCost(totalCost)}}</span>
  <span class="unit">USD</span>
</div>
<div>
  <span class="big">{{totalLand}}</span>
  <span class="unit">ha</span>
</div>
<div>
  <span class="big">{{totalEnergy}}</span>
  <span class="unit">EJ</span>
</div>

<div class="caps-label">COST</div>
<div class="caps-label">LAND</div>
<div class="caps-label">ENERGY</div>

</section>


<h3>Solutions</h3>
<p class="bright">Move the sliders until you achieve 10 Gigaton scale.</p>

<section style="display:grid; grid-template-columns: 1fr 1fr">
<v-slider
  v-model="tonsAllocated[solution]"
  v-for="solution in solutions"
  :key="solution"
  :hint="solution"
  :max="maxAlloc"
  :min="minAlloc"
  thumb-label="always"
  style="min-width: 200px"
  persistent-hint
></v-slider>
</section>



<h3>Simulated news</h3>
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

      totalSequestered(){
        return {
          value: _.sum(_.values(this.tonsAllocated)),
          unit: 'MT'
        };
      },

      totalCost(){
        // TODO(John): Pick a unit and return a number.
        return this.tonsAllocated.forests + this.tonsAllocated.dac;
      },
      totalLand(){
        // TODO(John): Pick a unit and return a number.
        return this.tonsAllocated.soil + this.tonsAllocated.beccs;
      },
      totalEnergy(){
        // TODO(John): Pick a unit and return a number.
        return 10 * (this.tonsAllocated.soil + this.tonsAllocated.forests);        
      },
      totalPermanence(){
        return [
          {name: '<50 years', value: this.tonsAllocated.soil * 2, unit: 'MT'},
          {name: '50-200 years', value: this.tonsAllocated.beccs * 2, unit: 'MT'},
          {name: '>200 years', value: this.tonsAllocated.dac * 2, unit: 'MT'},
        ]
      },

      newsItems(){
        // TODO: Add logic to return the right news items based on the values of the data variables.
        return [
          '2030: Food prices rising due to BECCS competition for land!',
          '2030: You’ve catalyzed a movement of farmers in the Corn Belt to move to regenerative agriculture!'
        ]
      }


    },
    data(){

      // TODO: John figure out what the units should be wtihin the code.
      return {
        solutions: ['forests', 'dac', 'beccs', 'soil'],
        tonsAllocated: {
          forests: 0,
          dac: 0,
          beccs: 0,
          soil: 0,          
        },

        minAlloc: 0,
        maxAlloc: TEN_BILLION,
      }
    },

    methods: {
      pprintCost(val){
        return '$' + val;
      },

      // pprintLand(val){

      // },

      // pprintEnergy(val){

      // }
    }
  }
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.bright {
  color: #ecc400;
  font-weight: bold;
}

.big{
  font-weight: bold;
  font-size: 3em;
}

.caps-label{
  font-weight:bold;
  opacity: 0.5;
}

.unit{
  margin-left: 4px;
  font-weight:bold;
  opacity: 0.5;
}


</style>