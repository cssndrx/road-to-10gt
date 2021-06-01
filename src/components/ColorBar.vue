<template>
<div class="outer">
	<div class="inner" :style="{width: (Math.min(frac,1)*100)+'%'}"></div>
</div>
</template>

<script>

export default {
	name: "ColorBar",
	props: {
		frac: {type: Number, default: 0.5},
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.outer{
	background-color: black
}

.inner{
	height: 24px; 
	background-color: #FFC107
}
</style>
