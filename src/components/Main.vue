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

			<v-col cols="6">
				<div>
					Your solution has
					<span class="bright" style="font-size: 1em;"
						>{{ getPermanenceLabel() }} permanence</span
					>
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
			<!-- <v-slider
  v-model="testing"
  :max="100"
  :min="0"
  :disabled="snackbar"
  thumb-label="always"
  style="min-width: 200px"
  persistent-hint
></v-slider> -->

			<!-- https://vuetifyjs.com/en/api/v-slider/#slots -->
			<v-slider
				v-model="tonsAllocated[solution]"
				v-for="solution in solutions"
				:key="solution"
				:hint="'$' + costPerTonEstimate(solution) + '/ton'"
				:max="maxAllocForSolution(solution)"
				:min="minAlloc"
				:thumb-size="16"
				:disabled="snackbar"
				thumb-label="always"
				style="min-width: 200px"
				persistent-hint
			>
				<span slot="label" class="solution-label">{{ pprintSolution(solution) }}</span>

				<span slot="thumb-label">{{ pprint("scale", tonsAllocated[solution]).big }}T</span>
			</v-slider>

			<!-- <input
type="range"
  v-model="tonsAllocated[solution]"
  v-for="solution in solutions"
  :key="solution"
  :hint="'$' + costPerTonEstimate(solution) + '/ton'"
  :max="maxAllocForSolution(solution)"
  :min="minAlloc"
  :thumb-size="16"
  :disabled="snackbar"
  thumb-label="always"
  style="min-width: 200px"
  persistent-hint
/> -->
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

				<div
					style="position:relative; bottom:12px; color: #777; font-size:0.8em; text-align: center"
				>
					{{ percentUtilization }}% utilized, {{ 100 - percentUtilization }}% sequestered
				</div>
			</v-col>
		</v-row>

		<h3 class="mt-5">Simulated news</h3>
		<div
			class="news"
			v-for="newsItem in newsItems"
			:key="newsItem.text"
			:style="{ color: newsItem.color }"
		>
			{{ newsItem.text }}
		</div>

		<v-row>
			<v-col cols="12">
				<v-img :src="require('../assets/world.png')" class="my-3" contain />
			</v-col>

			<v-col class="mb-4 col-md-8">
				<h1 class="display-3 font-weight-bold my-3">
					The Road to Ten Gigatons
				</h1>

				<p class="subheading font-weight-regular">
					According to the IPCC, we'll need to scale up carbon removal to reach 10 billion
					tons (or gigatons) of CO2 removed from the atmosphere by 2050. Choose carbon
					removal approaches to scale up by moving the sliders above until you reach 10
					gigatons, but watch out for the land and energy you're using in the process,
					along with other unintended consequences!
				</p>

				<v-btn class="ma-2 yellow darken-1 font-weight-black">
					PLAY
				</v-btn>

				<h3>Forests</h3>

				<p>
					The oldest carbon removal trick in the book. Planting more trees is fairly
					cheap, but it takes up a lot of land, uses a lot of water, and when the tree
					dies (by wildfire, disease, or getting cut down), the carbon stored will be
					released right back into the atmosphere.
				</p>

				<h3>Soil</h3>

				<p>
					Storing carbon in agricultural soils requires the farmers managing the land to
					adopt challenging regenerative practices like not tilling the soil, planting
					cover crops, and maintaining a diverse crop rotation. Large-scale behavior
					change is needed to scale up soil carbon sequestration, and if farmers end these
					practices, the carbon will be released back into the atmosphere. However, soil
					with more carbon stored in it is healthier and more and fertile.
				</p>

				<h3>Blue Carbon</h3>

				<p>
					Coastal blue carbon ecosystems like mangrove forests, seagrasses, and salt
					marshes have soils that can store immense amounts of carbon due to anoxic
					conditions, so protecting and restoring these ecosystems will have outsized
					positive climate impact.
				</p>

				<h3>DAC (Direct Air Capture)</h3>

				<p>
					Use chemical engineering wizardry to physically separate the CO2 out of the
					ambient air. Very energy-intensive and expensive to do, but the CO2 captured can
					be permanently sequestered.
				</p>

				<h3>BECCS (Bio-energy with Carbon Capture and Storage)</h3>

				<p>
					Plant fast-growing trees or crops at scale, but instead of letting them die and
					decompose (releasing their stored carbon), harvest and combust them to produce
					bio-energy, capturing the CO2 released on the spot and then sequestering it.
					Less costly and less energy-intensive than DAC (it actually produces energy),
					but growing dedicated crops for BECCS requires lots of land and resources.
				</p>

				<h3>Enhanced Weathering</h3>

				<p>
					Mining abundant rocks like olivine that are chemically reactive with CO2, and
					accelerating the rate at which these rocks react with atmospheric CO2.
				</p>
			</v-col>
		</v-row>

		<v-snackbar v-model="snackbar">
			{{ snackbarText }}

			<template v-slot:action="{ attrs }">
				<v-btn :color="snackbarColor" text v-bind="attrs" @click="snackbar = false">
					Accept
				</v-btn>
			</template>
		</v-snackbar>

		<v-dialog v-model="dialog" width="500">
			<v-card>
				<v-card-title class="headline">
					You win!
				</v-card-title>

				<v-card-text>
					<h3>
						Congratulations! You reached 10 Gigatons.
					</h3>

					<br />
					<h4>
						Note: {{ parseInt((100 * permanences[2].value) / tonsSequestered) }}% of the
						CO2 you removed has been stored long-term, but the rest will be re-emitted
						back into the atmosphere.
					</h4>
					<br />
					<h3>Here's what happened globally due to your carbon removal scale-up:</h3>
					<div
						class="news"
						v-for="newsItem in newsItems"
						:key="newsItem.text"
						:style="{ color: newsItem.color }"
					>
						{{ newsItem.text }}
					</div>
				</v-card-text>

				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn color="primary" text @click="dialog = false">
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

		estimates() {
			// TODO(John): Return actual estimates.
			return {
				cost: this.solutions.reduce((acc, sol) => acc + this.totalCostEstimate(sol), 0),
				land: this.solutions.reduce((acc, sol) => acc + this.landEstimate(sol), 0),
				energyUsed: this.solutions.reduce(
					(acc, sol) => acc + this.energyUsedEstimate(sol),
					0
				),
				energyProduced: this.solutions.reduce(
					(acc, sol) => acc + this.energyProducedEstimate(sol),
					0
				),
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

		// TODO(John): fill in actual constraints.
		constraintsViolated() {
			const constraints = [
				// {
				// 	condition: this.estimates.cost > 2,
				// 	text: "Too much money!",
				// 	color: "red",
				// },
				// {
				// 	condition: this.estimates.land > 2,
				// 	text: "Too much land!",
				// 	color: "red",
				// },
			];
			return constraints.filter(item => item.condition);
		},
	},
	watch: {
		constraintsViolated(violated) {
			violated.forEach(constraint => this.setAlert(constraint.text, constraint.color));
		},

		tonsSequestered() {
			if (this.tonsSequestered > TEN_BILLION) {
				this.dialog = true;
			}
		},
		newsItems(newNews, oldNews) {
			const olds = _.map(oldNews, "text");
			const news = _.map(newNews, "text");
			const additions = _.difference(news, olds);

			if (additions.length > 1) {
				console.error(
					"Expected only one additional news item, but got: " +
						additions.length +
						". Try staggering the thresholds for news items differently"
				);
			}

			// Turn the additions into toasts
			if (additions.length == 1) {
				const text = additions[0];
				const color = newNews.filter(news => news.text === text)[0].color;
				this.setAlert(text, color);
			}
		},
	},
	data() {
		return {
			testing: 0,

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

			// The bottom of screen snackbar toasts for news items.
			snackbar: false,
			snackbarText: "This is a test",
			snackbarColor: "green",

			// The end game dialog.
			dialog: false,
		};
	},

	methods: {
		// Set a blocking alert.
		setAlert(text, color) {
			this.snackbarColor = color;
			this.snackbarText = text;
			this.snackbar = true;
		},
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
		getPermanenceLabel() {
			const perms = this.permanences;
			const sum = perms.reduce((acc, x) => acc + x.value, 0);
			const fracs = perms.map(x => (sum === 0 ? 0 : x.value / sum));
			const weighted = Math.round(fracs[0] * 1 + fracs[1] * 2 + fracs[2] * 3);
			switch (weighted) {
				case 1:
					return "low";
				case 2:
					return "medium";
				case 3:
					return "high";
				default:
					return "medium";
			}
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
				{ limit: 1e9, suffix: "B" },
				{ limit: 1e6, suffix: "M" },
				{ limit: 1e3, suffix: "k" },
			];

			for (const lim of limits) {
				if (value >= lim.limit) {
					return (value / lim.limit).toFixed(1) + lim.suffix;
				}
			}
			return value.toFixed(1).toString();
		},
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