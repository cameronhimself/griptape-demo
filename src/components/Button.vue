<template>
	<button class="button" @click="$emit('click')">
		<span class="button-text"><slot /></span>
	</button>
</template>

<script>
	export default {
		props: {
			label: {},
		},
	};
</script>

<style lang="scss" scoped>
	.button {
		transition: background-color 0.2s;
		border: 1px solid #000;
		background: none;
		padding: 5px 10px;
		border-radius: 3px;
		margin: 0;
		background-color: rgba(80,80,80,0.8);
		color: #fff;
		font-size: 14px;
		// text-shadow: -1px -1px 1px rgba(0,0,0,0.5), 1px 1px 1px rgba(255,255,255,0.5);
		&:hover {
			background-color: rgba(110,110,110,0.8);
		}
	}
</style>
