<template>
	<div>
		<div v-show="viewingPattern" class="pattern" :style="{ backgroundImage: background }"></div>
		<div v-show="! viewingPattern" class="config-view">
			<pre>{{ configDisplay }}</pre>
		</div>
	</div>
</template>

<script>
	import griptape from 'griptape';

	export default {
		props: {
			config: {},
			viewingPattern: {},
			patternKey: {},
		},
		computed: {
			background() {
				return griptape[this.patternKey](this.config).toCSSURL();
			},
			configDisplay() {
				const configLines = Object.keys(this.config).reduce((acc, key) => {
					const val = JSON.stringify(this.config[key]).replace(/"/g, '\'');
					acc.push(`  ${key}: ${val},`);
					return acc;
				}, []);
				configLines.unshift(`griptape.${this.patternKey}({`);
				configLines.push('});');
				return configLines.join('\n');
			},
		},
	};
</script>

<style lang="scss">
	.pattern, .config-view {
		background-color: #fff;
		width: 100%;
		height: 300px;
	}
	.config-view {
		background: #000;
		pre {
			padding: 20px;
			margin: 0;
			color: #fff;
		}
	}
</style>
