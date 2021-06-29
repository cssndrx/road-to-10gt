<template>
	<div class="outer" :style="{ height: height + 'px' }">
		<div
			class="inner"
			:style="{ width: Math.max(1, Math.min(frac, 1) * 100) + '%', height: height + 'px' }"
		></div>
	</div>
</template>

<script>
export default {
	name: "ColorBar",
	props: {
		frac: { type: Number, default: 0.5 },
		height: { type: Number, default: 24 },
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
