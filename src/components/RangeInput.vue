<template>
	<span class="range">
		<span class="range-track">
			<span class="thumb"></span>
		</span>
	</span>
</template>

<script>
	export default {
		props: {
			value: {},
			min: {},
			max: {},
		},
	};
</script>

<style lang="scss" scoped>
	.range {
		display: inline-block;
		width: 129px;
	}

	.range-track {
		display: block;
		background: #f00;
		width: 100%;
		height: 3px;
	}

	.range-thumb {
	}
</style>
