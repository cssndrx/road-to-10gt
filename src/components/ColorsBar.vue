<template>
	<div class="outer" :style="{ height: height + 'px' }">
		<div
			class="inner"
			:style="{
				width: 100 + '%',
				height: height + 'px',
				backgroundImage: fracsToGradient,
			}"
		></div>
	</div>
</template>

<script>
export default {
	name: "ColorsBar",
	props: {
		fracs: { type: Object, default: () => ({}) },
		colors: { type: Object, default: () => ({}) },
		height: { type: Number, default: 24 },
	},
	computed: {
		fracsToGradient() {
			const solutionOrder = [
				"forests",
				"beccs",
				"blueCarbon",
				"dac",
				"soil",
				"enhancedWeathering",
			];
			const colorOrder = solutionOrder.map(x => this.colors[x]);
			colorOrder.push("black");
			const fracList = solutionOrder.map(x => this.fracs[x]);
			const percentageStops = fracList.map((current, idx, array) => {
				return array.slice(0, idx + 1).reduce((a, b) => a + b, 0) * 100;
			});
			let gradientString = `linear-gradient(90deg, ${colorOrder[0]}`;
			for (let stopIdx in percentageStops) {
				const stop = percentageStops[stopIdx];
				gradientString += `, ${colorOrder[stopIdx]} ${stop}%`;
				gradientString += `, ${colorOrder[parseInt(stopIdx) + 1]} ${stop}%`;
			}
			return gradientString + ")";
		},
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.outer {
	background-color: black;
}

.inner {
	background-color: #ffc107;
}
</style>
