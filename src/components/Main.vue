<template>
  <v-container>
    <h1 class="display-3 font-weight-bold mb-3">
      The Road to Ten Gigatons
    </h1>
    <h2 class="font-weight-regular display-1 mb-16">Carbon Removal Scale Up Challenge</h2>

    <v-row>
      <v-col cols="3" style="background-color: #777">
        <div>
          <span class="big">{{ pprint("scale", tonsSequestered).big }}</span>
          <span class="little">{{ pprint("scale", tonsSequestered).little }}</span>
        </div>
        <div class="caps-label">SCALE</div>
      </v-col>

      <v-col cols="1"></v-col>

      <v-col cols="6" v-if="phaseInd > 0">
        <div>
          Your solution has
          <span class="bright" style="font-size: 1em;">medium permanence</span>
          <div style="display:grid; grid-template-columns: 1fr 1fr 1fr">
            <div v-for="perm in permanences" :key="'val' + perm.name">
              <span class="text-h4">{{ pprint("scale", perm.value).big }}</span>
              <span class="little">{{ pprint("scale", perm.value).little }}</span>
            </div>

            <div v-for="perm in permanences" :key="'name' + perm.name">
              {{ perm.name }}
            </div>
          </div>
        </div>
      </v-col>
    </v-row>
    <!-- end top row -->

    <section class="mt-10" style="display:grid; grid-template-columns: 1fr 1fr 1fr 1fr;">
      <div v-for="dim in dimensions" :key="dim">
        <span class="big">{{ pprint(dim, estimates[dim]).big }}</span>
        <span class="little">{{ pprint(dim, estimates[dim]).little }}</span>
      </div>

      <div class="caps-label" v-for="dim in dimensions" :key="'label' + dim">{{ dim }}</div>

      <div v-for="dim in dimensions" :key="'context' + dim">{{ getContext(dim) }}</div>
    </section>

    <h3 class="mt-10" style="display:inline-block">Solutions</h3>
    <p class="bright" style="display:inline-block; margin-left: 16px;">
      Move the sliders until you achieve 10 Gigaton scale.
    </p>

    <section
        style="display:grid; grid-template-columns: 1fr 1fr; grid-column-gap: 10%; grid-row-gap: 24px;"
    >

            <v-slider
                v-model="tonsAllocated[solution]"
                v-for="solution in solutions"
                :key="solution"
                :hint="'$' + costPerTonEstimate(solution) + '/ton'"
                :max="maxAllocForSolution(solution)"
                :min="minAlloc"
                :thumb-size="16"
                thumb-label="always"
                style="min-width: 200px"
                persistent-hint
            >
              <span slot="label" class="solution-label">{{ pprintSolution(solution) }}</span>

              <span slot="thumb-label">{{ pprint("scale", tonsAllocated[solution]).big }}T</span>
            </v-slider>
    </section>

    <v-row class="mt-10" v-if="phaseInd >= 2">
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

        <div
            style="position:relative; bottom:12px; color: #777; font-size:0.8em; text-align: center"
        >
          {{ percentUtilization }}% utilized, {{ 100 - percentUtilization }}% sequestered
        </div>
      </v-col>
    </v-row>

    <h3 class="mt-5" v-if="newsItems.length > 0">With your current setup</h3>
    <div v-if="dealbreakers.length > 0">Dealbreakers (you must resolve these to win):</div>
    <div
        class="news"
        v-for="newsItem in dealbreakers"
        :key="newsItem.text"
        :style="{ color: 'red' }"
    >
      {{ newsItem.text }}
    </div>

    <div v-if="warnings.length > 0">Warnings:</div>
    <div
        class="news"
        v-for="newsItem in warnings"
        :key="newsItem.text"
        :style="{ color: 'yellow' }"
    >
      {{ newsItem.text }}
    </div>

    <div v-if="goodNews.length > 0">Good news:</div>
    <div
        class="news"
        v-for="newsItem in goodNews"
        :key="newsItem.text"
        :style="{ color: 'green' }"
    >
      {{ newsItem.text }}
    </div>

    <v-row>
      <v-col cols="12">
        <v-img :src="require('../assets/world.png')" class="my-3" contain/>
      </v-col>

      <v-col class="mb-4 col-md-8">
        <h1 class="display-3 font-weight-bold my-3">
          The Road to Ten Gigatons
        </h1>

        <p class="subheading font-weight-regular">
          <strong>Play to save Earth</strong> by removing 10 Gigatons of CO2 with these
          solutions. Todo provide more context around why.
        </p>

        <v-btn class="ma-2 yellow darken-1 font-weight-black">
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
      </v-col>
    </v-row>

    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="headline">
          You win!
        </v-card-title>

        <v-card-text>
          <h3 v-if="!isLastPhase">
            Congratulations! You reached 10 Gigatons in the {{ phaseNames[phaseInd] }} phase.
          </h3>
          <h3 v-else>
            Congratulations! You won the game.
          </h3>

          <p>
            ...Describe the phase you won and the next phase...
          </p>

          <br/>
          <h4>
            Note: {{ parseInt((100 * permanences[2].value) / tonsSequestered) }}% of the
            CO2 you removed has been stored long-term, but the rest will be re-emitted
            back into the atmosphere.
          </h4>
          <br/>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="nextPhase()" v-if="!isLastPhase">
            Proceed to {{ phaseNames[phaseInd + 1] }} phase;
          </v-btn>
          <v-btn color="primary" text @click="resetGame()" v-else>
            Play again
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import _ from "lodash";

const MILLION = 1000000;
const BILLION = 1000000000;
const TEN_BILLION = 10000000000;

export default {
  name: "Main",

  computed: {
    tonsSequestered() {
      return _.sum(_.values(this.tonsAllocated));
    },
    isWin() {
      if (this.tonsSequestered < TEN_BILLION) {
        return false;
      }
      if (this.dealbreakers.length > 0) {
        return false;
      }

      if (this.phaseInd >= 1) {
        // Fail on Phase 1 conditions...
        if (this.phaseInd >= 2) {
          // Fail on Phase 2 conditions...
        }
      }
      return true;
    },
    estimates() {
      // TODO(John): Return actual estimates.
      return {
        cost: _.sumBy(this.solutions, this.totalCostEstimate),
        land: _.sumBy(this.solutions, this.landEstimate),
        energyUsed: _.sumBy(this.solutions, this.energyUsedEstimate),
        energyProduced: _.sumBy(this.solutions, this.energyProducedEstimate),
      };
    },
    permanences() {
      // TODO(John): Return actual estimates.
      const tonsCaptured = this.tonsAllocated.beccs + this.tonsAllocated.dac;
      const permanentTons = (1 - this.percentUtilization / 100) * tonsCaptured;
      const impermanentTons = (this.percentUtilization / 100) * tonsCaptured;

      return [
        {
          name: "<50 years",
          value:
              this.tonsAllocated.forests / 2 +
              this.tonsAllocated.soil +
              impermanentTons * 0.5,
        },
        {
          name: "50-200 years",
          value:
              this.tonsAllocated.forests / 2 +
              impermanentTons * 0.1 +
              this.tonsAllocated.blueCarbon,
        },
        {
          name: ">200 years",
          value:
              this.tonsAllocated.enhancedWeathering +
              permanentTons +
              impermanentTons * 0.4,
        },
      ];
    },

    newsItems() {
      // TODO(John): Add logic to return the right news items based on the values of the data variables.

      // '2030: Food prices rising due to BECCS competition for land!',
      // '2030: You’ve catalyzed a movement of farmers in the Corn Belt to move to regenerative agriculture!',

      const possibleNewsItems = [
        {
          condition: this.tonsAllocated.forests > 0.75 * BILLION,
          text: "Trees planted in the tropics have dramatically increased air quality!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.forests > 2 * BILLION,
          text: "Trees introduced for carbon removal are invasive species in Indonesia!",
          color: "red",
        },
        {
          condition: this.tonsAllocated.forests > 2.5 * BILLION,
          text:
              "Wildfires burn down many of the trees that were used for carbon removal, releasing that CO2 back into the atmosphere!",
          color: "red",
        },
        {
          condition: this.tonsAllocated.soil > 1.5 * BILLION,
          text:
              "Regenerative agricultural practices phase out ammonia fertilizer, continuing to reduce CO2 emissions!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.soil > 2 * BILLION,
          text:
              "Due to the mass adoption of regenerative practices, crop yields around the world have increased!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.soil > 3 * BILLION,
          text:
              "Large groups of farmers begin to till their land again, releasing much of the carbon stored in these soils back into the atmosphere!",
          color: "red",
        },
        {
          condition: this.tonsAllocated.beccs > 1 * BILLION,
          text:
              "Using waste biomass alone, BECCS provides enough energy to meet Australia’s energy demand!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.beccs > 6.5 * BILLION,
          text:
              "Despite using vast amounts of land to cultivate dedicated energy crops, BECCS provides enough energy to meet half of the US’s energy demand!",
          color: "yellow",
        },
        {
          condition: this.estimates.land > 1.2 * Math.pow(10, 8),
          text:
              "Food prices skyrocket and food scarcity becomes a major worldwide problem as carbon removal takes up increasing land area.",
          color: "red",
        },
        {
          condition: this.tonsAllocated.beccs + this.tonsAllocated.forests > 5 * BILLION,
          text:
              "Freshwater shortages become an international concern as newly planted forests and energy crops for BECCS consume ever increasing amounts of water.",
          color: "red",
        },
        {
          condition: this.tonsAllocated.blueCarbon > 0.25 * BILLION,
          text:
              "Restoration of coastal ecosystems has increased protection against yearly coastal flooding!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.blueCarbon > 0.5 * BILLION,
          text:
              "Due to extensive protection for coastal restoration, the Western Mediterrean and the Gulf Coast regions have seen a decrease in tourism.",
          color: "yellow",
        },
        {
          condition: this.tonsAllocated.blueCarbon > 0.75 * BILLION,
          text:
              "Coastal ecosystems provide a natural buffer against climate induced storms and natural disasters worldwide!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.enhancedWeathering > 2,
          text: "Agricultural yields increase from added mineral nutrients!",
          color: "green",
        },
        {
          condition: this.tonsAllocated.soil > 4 * BILLION,
          text:
              "Land used for soil carbon sequestration has started to release methane into the atmosphere!",
          color: "red",
        },
        {
          condition: this.estimates.energyUsed > 53,
          text:
              "Energy used for direct air capture makes up more than half of total US energy consumption in 2020!",
          color: "red",
        },
      ];
      return possibleNewsItems.filter(item => item.condition);
    },

    goodNews() {
      return this.newsItems.filter(_ => _.color === 'green');
    },
    warnings() {
      return this.newsItems.filter(_ => _.color === 'yellow');
    },
    dealbreakers() {
      return this.newsItems.filter(_ => _.color === 'red');
    },

    isLastPhase() {
      return this.phaseInd == this.phaseNames.length - 1;
    }
  },
  watch: {
    isWin() {
      this.dialog = true;
    },
  },

  data() {
    return {
      phaseNames: ["intro", "permanence", "utilization"],
      phaseInd: 0,

      dimensions: ["cost", "land", "energyUsed", "energyProduced"],
      solutions: ["forests", "dac", "beccs", "soil", "blueCarbon", "enhancedWeathering"],
      tonsAllocated: {
        forests: 0,
        dac: 0,
        beccs: 0,
        soil: 0,
        blueCarbon: 0,
        enhancedWeathering: 0,
      },

      minAlloc: 0,
      maxAlloc: TEN_BILLION,

      percentUtilization: 50,

      // The end game dialog.
      dialog: false,
    };
  },

  methods: {
    getContext(dim) {
      // TODO(John): Return string with context for string dim ('cost', 'energy', 'land')
      // You can look up the values using this.estimates[dim]
      if (this.estimates[dim] === 0) {
        return "";
      }
      if (dim === "cost") {
        return `~${Number((100 * this.estimates[dim]) / (21 * Math.pow(10, 12))).toFixed(
            1
        )}% of United States GDP in 2020`;
      } else if (dim === "land") {
        return `~${Number((100 * this.estimates[dim]) / (328.7 * MILLION)).toFixed(
            1
        )}% the land area of India`;
      } else if (dim === "energyUsed" || dim === "energyProduced") {
        // const energyBreakpoints = {
        // 	6.61: "California",
        // 	31.7: "Europe",
        // 	105.7: "the United States",
        // };
        return `~${Number((100 * this.estimates[dim]) / 31.7).toFixed(
            1
        )}% the annual energy consumption of Europe`;
      } else {
        return "";
      }
    },
    maxAllocForSolution(sol) {
      return {
        forests: 3 * BILLION,
        soil: 5 * BILLION,
        beccs: TEN_BILLION,
        dac: TEN_BILLION,
        blueCarbon: BILLION,
        enhancedWeathering: 4 * BILLION,
      }[sol];
    },
    // Return the cost per ton (can be dynamic).
    costPerTonEstimate(sol) {
      // TODO(John): Return actual estimates.
      return {
        forests: 20,
        beccs: this.tonsAllocated.beccs < 4 * BILLION ? 150 : 300,
        dac:
            this.tonsAllocated.dac < BILLION
                ? 600
                : Math.floor(600 * Math.pow(this.tonsAllocated.dac / BILLION, -0.5146)),
        soil: 15,
        enhancedWeathering: 80,
        blueCarbon: 30,
      }[sol];
    },
    totalCostEstimate(sol) {
      // integrating the cost functions above
      // (not sure if there's a JS way to do this)
      const dacIntegrated = x => {
        // integrating the polynomial DAC learning curve function
        return 5.28995 * Math.pow(10, 7) * Math.pow(x, 0.4854);
      };

      return {
        forests: 20 * this.tonsAllocated.forests,
        soil: 15 * this.tonsAllocated.soil,
        blueCarbon: 30 * this.tonsAllocated.blueCarbon,
        enhancedWeathering: 80 * this.tonsAllocated.enhancedWeathering,
        dac:
            this.tonsAllocated.dac < BILLION
                ? 600 * this.tonsAllocated.dac
                : 600 * BILLION +
                (dacIntegrated(this.tonsAllocated.dac) - dacIntegrated(BILLION)),
        beccs:
            this.tonsAllocated.beccs < 4 * BILLION
                ? 150 * this.tonsAllocated.beccs
                : 150 * 4 * BILLION + 600 * (this.tonsAllocated.beccs - 4 * BILLION),
      }[sol];
    },
    landEstimate(sol) {
      // all estimates in ha / ton
      return {
        forests: 0.11 * this.tonsAllocated.forests,
        soil: 0, // no additional land use
        beccs:
            this.tonsAllocated.beccs < 4 * BILLION
                ? 0.003 * this.tonsAllocated.beccs
                : 0.003 * 4 * BILLION + 0.06 * (this.tonsAllocated.beccs - 4 * BILLION),
        dac: this.tonsAllocated.dac * Math.pow(2.87904, -4),
        blueCarbon: (1 / 4.78) * this.tonsAllocated.blueCarbon,
        enhancedWeathering: (0.61 / 1000) * this.tonsAllocated.enhancedWeathering,
      }[sol];
    },
    energyUsedEstimate(sol) {
      // all the dividing by BILLION due to conversion from GJ to EJ
      return {
        forests: 0,
        soil: 0,
        blueCarbon: 0,
        beccs: 0,
        dac: (6.88 / BILLION) * this.tonsAllocated.dac,
        enhancedWeathering: (6 / BILLION) * this.tonsAllocated.enhancedWeathering,
      }[sol];
    },

    energyProducedEstimate(sol) {
      // all the dividing by BILLION due to conversion from GJ to EJ
      return {
        forests: 0,
        soil: 0,
        blueCarbon: 0,
        beccs: (8 / BILLION) * this.tonsAllocated.beccs,
        dac: 0,
        enhancedWeathering: 0,
      }[sol];
    },
    // Pretty print solution string.
    pprintSolution(sol) {
      return {
        forests: "Forests",
        dac: "DAC",
        beccs: "BECCS",
        soil: "Soil",
        blueCarbon: "Blue Carbon",
        enhancedWeathering: "Enhanced Weathering",
      }[sol];
    },

    // Pretty print big numbers.
    pprint(key, val) {
      const littles = {
        scale: "tons",
        cost: "USD",
        land: "ha",
        energyUsed: "EJ",
        energyProduced: "EJ",
      };

      const prefix = key === "cost" ? "$" : "";
      return {
        big: prefix + this.formatNumber(val),
        little: littles[key],
      };
    },

    // Helper function for formatting big numbers.
    formatNumber(value) {
      var limits = [
        {limit: 1e9, suffix: "B"},
        {limit: 1e6, suffix: "M"},
        {limit: 1e3, suffix: "k"},
      ];

      for (const lim of limits) {
        if (value >= lim.limit) {
          return (value / lim.limit).toFixed(1) + lim.suffix;
        }
      }
      return value.toFixed(1).toString();
    },

    nextPhase() {
      this.phaseInd++;
      this.dialog = false;
    },
    resetGame() {
      this.phaseInd = 0;
      this.dialog = false;
      // TODO Reset everything else to initial state...
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.bright {
  color: #ecc400;
  font-weight: bold;
  font-size: 0.8em;
}

.big {
  font-weight: bold;
  font-size: 3em;
}

.caps-label {
  font-weight: bold;
  opacity: 0.5;
  text-transform: uppercase;
}

.unit {
  margin-left: 4px;
  font-weight: bold;
  opacity: 0.5;
}

.solution-label {
  text-transform: capitalize;
  font-size: 12px;
}

.news {
  margin-top: 8px;
}
</style>
