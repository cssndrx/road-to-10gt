<template>
	<v-container style="max-width: 1600px">
		<v-row>
			<v-col :md="8">
				<h1 class="display-3 font-weight-bold mb-3">
					The Road to Ten Gigatons
				</h1>
				<h2 class="font-weight-regular display-1 mb-16">
					Carbon Removal Scale Up Challenge
				</h2>
				<h3>Instructions</h3>
				<p class="subheading font-weight-regular mr-8">
					According to the IPCC (Intergovernmental Panel on Climate Change), we'll need to
					remove 10 billion tons (or gigatons) of CO2 from the atmosphere by 2050. Your
					task is to choose carbon removal approaches to scale up by moving the colored
					sliders below until you reach 10 gigatons, but without using too much land or
					energy, and without causing any dire unintended consequences! You'll win the
					game when you have reached 10 gigaton scale without having any dealbreakers in
					the consequences panel!
				</p>

				<h3>Active goals</h3>
					<div :key="datum.text" v-for="datum in winCriteria" style="font-weight: bold">
						{{datum.condition ? '❌': '✅' }} {{datum.text}}
					</div>

				<!-- 		<v-btn class="amber rounded-0 font-weight-black">
			PLAY
		</v-btn>
 -->
			</v-col>

			<v-col :md="4">
				<v-img
					:src="require('../assets/world.png')"
					style="max-width:300px; opacity: 0.4"
					contain
				/>
			</v-col>
		</v-row>

		<v-row class="justify-start">
			<v-col cols="4" :lg="2" class="ma-lg-4">
				<!-- 				<div style="background-color: #444; display:inline-block; padding: 16px">
 -->
				<div>
					<span class="big">{{ pprint("scale", tonsSequestered).big }}</span>
					<span class="little">{{ pprint("scale", tonsSequestered).little }}</span>
				</div>
				<div class="caps-label">SCALE</div>
				<!-- <ColorBar :frac="tonsSequestered / TEN_BILLION"></ColorBar> -->
				<ColorsBar :fracs="tonsAllocatedFracs" :colors="colorForSolution"></ColorsBar>
				<div class="context-text">
					{{ ((tonsSequestered / TEN_BILLION) * 100).toFixed(0) }}% of the way to 10GT
				</div>

			</v-col>

			<v-col cols="4" :lg="2" class="ma-lg-4" v-for="dim in dimensions" :key="dim">
				<span class="big">{{ pprint(dim, estimates[dim]).big }}</span>
				<span class="little">{{ pprint(dim, estimates[dim]).little }}</span>
				<div class="caps-label">{{ pprintDim(dim) }}</div>
				<!-- <ColorBar :frac="getContextNum(dim)"></ColorBar> -->
				<ColorsBar :fracs="getFracsForDim(dim)" :colors="colorForSolution"></ColorsBar>
				<div class="context-text">{{ getContext(dim) }}</div>
			</v-col>
		</v-row>
		<!-- end top row -->

		<v-row>
			<v-col cols="12" :lg="6">
				<h3>Solutions</h3>
<!-- 				<p class="bright mb-10">
					{{ goalMessage }}
				</p>
 -->
				<section class="solutions-grid">
					<template v-for="solution in solutions">
						<v-tooltip bottom :key="solution + 'label'" max-width="300" color="black">
							<template v-slot:activator="{ on, attrs }">
								<div v-bind="attrs" v-on="on" class="tooltip-target">
									<div class="solution-label">
										{{ pprintSolution(solution) }}
										<i class="fas fa-info-circle"></i>
									</div>

									<div class="solution-label">
										${{ costPerTonEstimate(solution) }}/ton
									</div>
								</div>
							</template>
							<span>{{ getTooltip(solution) }}</span>
						</v-tooltip>

						<v-slider
							v-model="tonsAllocated[solution]"
							:key="solution"
							:max="maxAllocForSolution(solution)"
							:min="minAlloc"
							:thumb-size="16"
							:color="colorForSolution[solution]"
							:thumb-color="colorForSolution[solution]"
							thumb-label="always"
							style="min-width: 200px"
							persistent-hint
							:validation="[tonsSequestered < TEN_BILLION || 'Scaled beyond 10 GT!']"
						>
							<span slot="thumb-label" class="thumb-label"
								>{{ pprint("scale", tonsAllocated[solution]).big }}T</span
							>
						</v-slider>
					</template>

				</section>

				<div v-if="phaseInd >= 1">

					<h3>Permanence challenge</h3>

					<div style="display:grid; grid-template-columns: 1fr 1fr; grid-gap: 5%">


					<div class="context-text">
						<strong
							>Your solution has
							<span class="bright" style="font-size: 1em; margin-top:28px">{{
								permanenceLabel
							}}</span>
							permanence</strong
						>
						<div style="display:grid; grid-template-columns: 1fr 1.5fr; align-items: end;">
							<template v-for="perm in permanences">
								<div style="font-size:12px; margin-top: 4px" :key="'name' + perm.name">
									{{ perm.name }}
								</div>
								<ColorBar
									:height="12"
									:key="perm.name + 'bar'"
									:frac="perm.value / TEN_BILLION"
								></ColorBar>
							</template>
						</div>
					</div>


					<div>
					<v-tooltip right max-width="300" color="black">
						<template v-slot:activator="{ on, attrs }">
							<div v-bind="attrs" v-on="on" class="tooltip-target">
								<div class="context-text"><strong>What happens to the captured CO<sub>2</sub>?</strong><i class="fas fa-info-circle" style="margin-left:4px"></i></div>
							</div>
						</template>
						<span
							>CO<sub>2</sub> captured with DAC or BECCS can either be sequestered
							underground permanently, or utilized to make useful and profitable
							products that will re-release the CO<sub>2</sub> back into the
							atmosphere.</span
						>
					</v-tooltip>

					<br />
					<v-slider
						v-model="percentUtilization"
						:min="0"
						:max="100"
						:thumb-size="16"
						color="yellow"
						thumb-color="amber"
						thumb-label="always"
						persistent-hint
					>
						<span slot="thumb-label" class="thumb-label" style="white-space:nowrap; left: 76px">{{ percentUtilization }}% utilized, {{ 100 - percentUtilization }}%
						sequestered</span>
					</v-slider>
					</div> 

				</div> 
			</div>
			</v-col>

			<v-col cols="12" :lg="6">
				<h3>Scale-up consequences</h3>
<!-- 				<p class="bright">
					Remove carbon without generating any dealbreakers.
				</p>
 -->
				<div class="news-header" v-if="dealbreakers.length > 0">
					Dealbreakers <span class="bright">(you must resolve these to win)</span>
				</div>
				<ul>
					<li class="news" v-for="newsItem in dealbreakers" :key="newsItem.text">
						<div
							style="display:inline-block;height: 20px; width: 20px; margin-right: 5px; position: relative; top: 5px;"
							:style="{ background: colorForSolution[newsItem.solution] }"
						></div>
						{{ newsItem.text }}
					</li>
				</ul>

				<div class="news-header" v-if="warnings.length > 0">Warning!</div>
				<ul>
					<li class="news" v-for="newsItem in warnings" :key="newsItem.text">
						<div
							style="display:inline-block;height: 20px; width: 20px; margin-right: 5px; position: relative; top: 5px;"
							:style="{ background: colorForSolution[newsItem.solution] }"
						></div>
						{{ newsItem.text }}
					</li>
				</ul>

				<div class="news-header" v-if="goodNews.length > 0">Good news</div>
				<ul>
					<!-- :style="{ color: colorForSolution[newsItem.solution] }" -->
					<li class="news" v-for="newsItem in goodNews" :key="newsItem.text">
						<div
							style="display:inline-block;height: 20px; width: 20px; margin-right: 5px; position: relative; top: 5px;"
							:style="{ background: colorForSolution[newsItem.solution] }"
						></div>
						{{ newsItem.text }}
					</li>
				</ul>
			</v-col>
		</v-row>

		<v-dialog v-model="dialog" width="500" persistent>
			<v-card>
				<v-card-title class="headline">
					You win!
				</v-card-title>

				<v-card-text>
					<h3>
						{{ headerCongratsMessage }}
					</h3>

					<div v-for="msg in congratulationsMessage" :key="'msg' + msg[0]">
						<br />
						<p>{{ msg }}</p>
					</div>
					<br />
				</v-card-text>

				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn color="primary" text @click="nextPhase()" v-if="!isLastPhase">
						Proceed to {{ phaseNames[phaseInd + 1] }} phase
					</v-btn>
					<div v-else>
						<v-btn color="primary" text @click="stayHere()">
							Stay here
						</v-btn>
						<v-btn color="primary" text @click="resetGame()">
							Play again
						</v-btn>
					</div>
				</v-card-actions>
			</v-card>
		</v-dialog>
	</v-container>
</template>

<script>
import _ from "lodash";
import ColorBar from "@/components/ColorBar.vue";
import ColorsBar from "@/components/ColorsBar.vue";

const MILLION = 1000000;
const BILLION = 1000000000;
const TEN_BILLION = 10000000000;

function getInitialState() {
	return {
		forests: 0,
		dac: 0,
		beccs: 0,
		soil: 0,
		blueCarbon: 0,
		enhancedWeathering: 0,
	};
}

export default {
	name: "Main",

	components: {
		ColorBar,
		ColorsBar,
	},
	computed: {
		tonsAllocatedFracs() {
			const fracs = {};
			for (const [key, value] of Object.entries(this.tonsAllocated)) {
				fracs[key] = value / TEN_BILLION;
			}
			return fracs;
		},
		tonsSequestered() {
			return _.sum(_.values(this.tonsAllocated));
		},
		permanenceLabel() {
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

		winCriteria(){
			var criteria = [
			{
				condition: this.tonsSequestered < TEN_BILLION,
				text: "Reach 10 gigatons of carbon removal",
			},
			{
				condition: this.dealbreakers.length > 0,
				text: "No dealbreaker scale-up consequences",
			},
			];

			if (this.phaseInd >= 1) {
				criteria.push(
					{
						condition: this.permanenceLabel !== "high",
						text: "Achieve high permanence in stored carbon",
					},
				);
			}
			return criteria;

		},
		isWin() {
			return this.winCriteria.filter(item => item.condition).length == 0;
		},
		estimates() {
			// TODO(John): Return actual estimates.
			return {
				cost: _.sumBy(this.solutions, this.totalCostEstimate),
				repurposedLand: _.sumBy(this.solutions, this.landEstimate),
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
		goalMessage() {
			if (this.phaseInd === 0) {
				return "Goal: Move the sliders to scale up different solutions until you achieve 10 Gigaton scale.";
			} else {
				return "Goal: Reach 10 gigatons with a high permanence solution. Hint: see the new slider below!";
			}
		},
		headerCongratsMessage() {
			if (this.phaseInd === 0) {
				return "Congratulations! You reached 10 Gigatons with no major negative consequences! However...";
			} else if (this.phaseInd === 1) {
				return "Congratulations! You reached 10 Gigatons with no major negative consequences, and with a high permanence solution. You win!";
			}
			return "";
		},
		congratulationsMessage() {
			if (this.phaseInd === 0) {
				return [
					`Only ${parseInt(
						(100 * this.permanences[2].value) / this.tonsSequestered
					)}% of the CO2 you removed has been stored long-term, and the rest will be re-emitted back into the atmosphere. In this next phase, modify your solution to be “high permanence”. You can monitor the permanence of your solution at top of the screen.`,
					`Right now, half of the CO2 captured via DAC and BECCS is being stored permanently. The other half is being used to make products that will be profitable and bring money into the industry, but will also release that CO2 back into the atmosphere on a short timeframe. You can modify the amount of CO2 being sequestered vs utilized with a new slider at the bottom of the screen.`,
				];
			} else if (this.phaseInd === 1) {
				return [
					`Your solution uses ${this.getContext("repurposedLand")}, ${this.getContext(
						"energyUsed"
					)}, and ${this.getContext(
						"cost"
					)}. Think you can do better? Give it another try!`,
				];

				// return [
				// 	`Right now your solution costs $${this.estimates["cost"]}, converts ${this.estimates["repurposedLand"]} hectares of land to carbon removal, and uses ${this.estimates["energyUsed"]} exajoules of energy. Think you can do better? Give it another try!`,
				// ];

				// DOesn't work for some reason.
				// return [`Right now your solution costs ${ this.pprint('cost', this.estimates['cost']).big } USD, converts ${ this.pprint('land', this.estimates['land']).big } hectares of land to carbon removal, and uses ${ this.pprint('energy', this.estimates['energy']).big } exajoules of energy. Think you can do better?`];
			}
			return [];
		},

		newsItems() {
			// TODO(John): Add logic to return the right news items based on the values of the data variables.

			// '2030: Food prices rising due to BECCS competition for land!',
			// '2030: You’ve catalyzed a movement of farmers in the Corn Belt to move to regenerative agriculture!',

			const possibleNewsItems = [
				{
					condition: this.tonsAllocated.forests > 0.75 * BILLION,
					text: "Trees planted in the tropics have dramatically increased air quality!",
					type: "good",
					solution: "forests",
				},
				{
					condition: this.tonsAllocated.forests > 2 * BILLION,
					text:
						"Some trees introduced for carbon removal have become an invasive species.",
					type: "warning",
					solution: "forests",
				},
				{
					condition: this.tonsAllocated.forests > 2.5 * BILLION,
					text:
						"Wildfires burn down many of the trees that were used for carbon removal, releasing that CO2 back into the atmosphere!",
					type: "warning",
					solution: "forests",
				},
				{
					condition: this.tonsAllocated.soil > 0.5 * BILLION,
					text: "Soil health already improving dramatically.",
					type: "good",
					solution: "soil",
				},
				{
					condition: this.tonsAllocated.soil > 1.5 * BILLION,
					text:
						"Regenerative agricultural practices phase out ammonia fertilizer, continuing to reduce CO2 emissions!",
					type: "good",
					solution: "soil",
				},
				{
					condition: this.tonsAllocated.soil > 2 * BILLION,
					text:
						"Due to the mass adoption of regenerative practices, crop yields around the world have increased!",
					type: "good",
					solution: "soil",
				},
				{
					condition: this.tonsAllocated.soil > 3 * BILLION,
					text:
						"Large groups of farmers begin to till their land again, releasing much of the carbon stored in these soils back into the atmosphere!",
					type: "warning",
					solution: "soil",
				},
				{
					condition: this.tonsAllocated.soil > 4 * BILLION,
					text:
						"Land used for soil carbon sequestration is beginning to release methane into the atmosphere.",
					type: "dealbreaker",
					solution: "soil",
				},
				{
					condition: this.tonsAllocated.beccs > 1 * BILLION,
					text:
						"Using waste biomass alone, BECCS provides enough energy to meet Australia’s energy demand!",
					type: "good",
					solution: "beccs",
				},
				{
					condition: this.tonsAllocated.beccs > 3 * BILLION,
					text:
						"Running out of waste biomass to use for BECCS, so beginning to switch to growing dedicated energy crops. This will require much more land...",
					type: "warning",
					solution: "beccs",
				},
				{
					condition: this.tonsAllocated.beccs > 6.5 * BILLION,
					text:
						"Despite using vast amounts of land to cultivate dedicated energy crops, BECCS provides enough energy to meet half of the US’s energy demand!",
					type: "good",
					solution: "beccs",
				},
				{
					condition: this.estimates.repurposedLand > 1.2 * Math.pow(10, 8),
					text:
						"Too much land usage! Food prices skyrocket and food scarcity becomes a major worldwide problem as carbon removal takes up increasing land area.",
					type: "dealbreaker",
					solution: "forests",
				},
				{
					condition: this.tonsAllocated.beccs + this.tonsAllocated.forests > 5 * BILLION,
					text:
						"Freshwater shortages become an international concern as newly planted forests and energy crops for BECCS consume ever increasing amounts of water.",
					type: "dealbreaker",
					solution: "beccs",
				},
				{
					condition: this.tonsAllocated.blueCarbon > 0.25 * BILLION,
					text:
						"Restoration of coastal ecosystems has increased protection against yearly coastal flooding!",
					type: "good",
					solution: "blueCarbon",
				},
				{
					condition: this.tonsAllocated.blueCarbon > 0.5 * BILLION,
					text:
						"Coastal communities living near the mangroves feel insufficiently engaged in some of the decision making processes, so you'll need to continue to work with them individually to ensure environmental justice.",
					type: "warning",
					solution: "blueCarbon",
				},
				{
					condition: this.tonsAllocated.blueCarbon > 0.75 * BILLION,
					text:
						"Coastal ecosystems provide a natural buffer against climate induced storms and natural disasters worldwide!",
					type: "good",
					solution: "blueCarbon",
				},
				{
					condition: this.tonsAllocated.enhancedWeathering > 1 * BILLION,
					text:
						"Mining of rocks for weathering is already on the order of billions of tons, and grinding of the mined rock also requires very substantial energy.",
					type: "warning",
					solution: "enhancedWeathering",
				},
				{
					condition: this.tonsAllocated.enhancedWeathering > 2 * BILLION,
					text:
						"Agricultural yields increase from added mineral nutrients, which also help provide crops protection from pests and diseases.",
					type: "good",
					solution: "enhancedWeathering",
				},
				{
					condition: this.tonsAllocated.dac > 1 * BILLION,
					text:
						"After lots of R&D work you've developed new advanced sorbent materials for capturing carbon and have mobilized global supply chains to produce modular DAC units. Costs of direct air capture are slowly beginning to come down.",
					type: "good",
					solution: "dac",
				},
				{
					condition: this.tonsAllocated.dac > 4 * BILLION,
					text:
						"The use of fossil fuel energy for direct air capture is contributing to air pollution in front-line communities. Note that using renewables requires much more land per ton captured.",
					type: "warning",
					solution: "dac",
				},
				{
					condition: this.estimates.energyUsed > 53,
					text:
						"Energy used for carbon removal makes up more than half of total US energy consumption in 2020!",
					type: "dealbreaker",
					solution: "dac",
				},
			];
			return possibleNewsItems.filter(item => item.condition);
		},

		goodNews() {
			return this.newsItems.filter(_ => _.type === "good");
		},
		warnings() {
			return this.newsItems.filter(_ => _.type === "warning");
		},
		dealbreakers() {
			return this.newsItems.filter(_ => _.type === "dealbreaker");
		},

		isLastPhase() {
			return this.phaseInd == this.phaseNames.length - 1;
		},
	},
	watch: {
		isWin() {
			if (this.isWin) {
				this.dialog = true;
			}
		},
	},

	data() {
		return {
			phaseNames: ["intro", "permanence"],
			phaseInd: 0,
			dimensions: ["cost", "repurposedLand", "energyUsed", "energyProduced"],
			solutions: ["forests", "dac", "beccs", "soil", "blueCarbon", "enhancedWeathering"],
			colorForSolution: {
				forests: "green",
				dac: "#ADD8E6",
				beccs: "#FF5722", // b19cd9
				soil: "#795548",
				blueCarbon: "#009688",
				enhancedWeathering: "#3F51B5",
			},
			// tonsAllocated: {
			// 	forests: 0,
			// 	dac: 0,
			// 	beccs: 0,
			// 	soil: 0,
			// 	blueCarbon: 0,
			// 	enhancedWeathering: 0,
			// },
			tonsAllocated: getInitialState(),

			minAlloc: 0,
			maxAlloc: TEN_BILLION,

			TEN_BILLION: TEN_BILLION,

			percentUtilization: 50,

			// The end game dialog.
			dialog: false,
		};
	},

	methods: {
		getFracsForDim(dim) {
			const fracs = {};
			const functionForDim = {
				cost: this.totalCostEstimate,
				repurposedLand: this.landEstimate,
				energyUsed: this.energyUsedEstimate,
				energyProduced: this.energyProducedEstimate,
			};
			for (let sol of this.solutions) {
				fracs[sol] = functionForDim[dim](sol) / this.getContextNumMax(dim);
			}
			return fracs;
		},
		pprintDim(dim) {
			const mapping = {
				repurposedLand: "Land",
				energyUsed: "Energy used",
				energyProduced: "Energy produced",
			};
			return mapping[dim] || dim;
		},

		getContext(dim) {
			// TODO(John): Return string with context for string dim ('cost', 'energy', 'land')
			// You can look up the values using this.estimates[dim]
			if (this.estimates[dim] === 0) {
				return "";
			}

			const pct = Number(this.getContextNum(dim) * 100).toFixed(1);
			if (dim === "cost") {
				return `${pct}% of United States GDP in 2020`;
			} else if (dim === "repurposedLand") {
				return `${pct}% the land area of India`;
			} else if (dim === "energyUsed" || dim === "energyProduced") {
				// const energyBreakpoints = {
				// 	6.61: "California",
				// 	31.7: "Europe",
				// 	105.7: "the United States",
				// };
				return `${pct}% the annual energy consumption of Europe`;
			} else {
				return "";
			}
		},
		getContextNum(dim) {
			return this.estimates[dim] / this.getContextNumMax(dim);
		},
		getContextNumMax(dim) {
			if (dim === "cost") {
				return 21 * Math.pow(10, 12);
			} else if (dim === "repurposedLand") {
				return 328.7 * MILLION;
			} else if (dim === "energyUsed" || dim === "energyProduced") {
				return 31.7;
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
		getTooltip(sol) {
			const tips = {
				forests: `The oldest carbon removal trick in the book. Planting more trees is fairly
					cheap, but it takes up a lot of land, uses a lot of water, and when the tree
					dies (by wildfire, disease, or getting cut down), the carbon stored will be
					released right back into the atmosphere.`,
				soil: `Storing carbon in agricultural soils requires the farmers managing the land to
					adopt challenging regenerative practices like not tilling the soil, planting
					cover crops, and maintaining a diverse crop rotation. Large-scale behavior
					change is needed to scale up soil carbon sequestration, and if farmers end these
					practices, the carbon will be released back into the atmosphere. However, soil
					with more carbon stored in it is healthier and more fertile.`,
				blueCarbon: `Coastal blue carbon ecosystems like mangrove forests, seagrasses, and salt
					marshes have soils that can store immense amounts of carbon due to anoxic
					conditions, so protecting and restoring these ecosystems will have outsized
					positive climate impact.`,
				beccs: `Plant fast-growing trees or crops at scale, but instead of letting them die and
					decompose (releasing their stored carbon), harvest and combust them to produce
					bio-energy, capturing the CO2 released on the spot and then sequestering it.
					Less costly and less energy-intensive than DAC (it actually produces energy),
					but growing dedicated crops for BECCS requires lots of land and resources.`,
				dac: `Use chemical engineering wizardry to physically separate the CO2 out of the
					ambient air. Very energy-intensive and expensive to do, but the CO2 captured can
					be permanently sequestered.`,
				enhancedWeathering: `Mining abundant rocks like olivine that are chemically reactive with CO2, and
					accelerating the rate at which these rocks react with atmospheric CO2, permanently capturing and storing it.`,
			};
			return tips[sol];
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
					this.tonsAllocated.beccs <= 4 * BILLION
						? 0.003 * this.tonsAllocated.beccs
						: 0.003 * 4 * BILLION + 0.06 * (this.tonsAllocated.beccs - 4 * BILLION),
				dac: this.tonsAllocated.dac * Math.pow(2.87904, -4),
				blueCarbon: 0, //(1 / 4.78) * this.tonsAllocated.blueCarbon,
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
				repurposedLand: "hectares",
				energyUsed: "exajoules",
				energyProduced: "exajoules",
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
				{ limit: 1e12, suffix: "T" },
				{ limit: 1e9, suffix: "B" },
				{ limit: 1e6, suffix: "M" },
				{ limit: 1e3, suffix: "k" },
			];

			const precision = value > BILLION ? 1 : 0;

			for (const lim of limits) {
				if (value >= lim.limit) {
					return (value / lim.limit).toFixed(precision) + lim.suffix;
				}
			}
			return value.toFixed(precision).toString();
		},

		nextPhase() {
			console.log("clicked");
			this.phaseInd++;
			this.dialog = false;
		},
		resetGame() {
			this.phaseInd = 0;
			this.dialog = false;
			this.tonsAllocated = getInitialState();
			this.percentUtilization = 50;
		},
		stayHere() {
			this.dialog = false;
		},
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.bright {
	color: #ecc400;
	font-weight: bold;
/* 	font-size: 0.8em;*/
}

.big {
	font-weight: bold;
	font-size: 3em;
	margin-right: 4px;
}

.caps-label {
	font-weight: bold;
	opacity: 0.8;
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

.news-header {
	font-weight: bold;
	margin-top: 8px;
}

.context-text {
	font-size: 12px;
	margin-top: 8px;
}

.tooltip-target {
	cursor: pointer;
}

.solutions-grid {
	display: grid;
	grid-template-columns: 1fr 3fr 1fr 3fr;
	grid-column-gap: 5%;
	grid-row-gap: 24px;
}

.thumb-label{
	position:relative; left: 16px; top:12px; font-size: 10px; text-align: left; font-weight:bold;
}

@media (max-width: 699px) {
	.solutions-grid {
		grid-template-columns: 1fr 3fr;
	}

	.big {
		font-size: 1.5em;
	}
}
</style>
