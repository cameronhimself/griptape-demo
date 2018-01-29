<template>
	<div class="root" :style="{ backgroundImage: background }">
		<div class="wrap">
			<div class="jumbotron">
				<div class="contain">
					<h1 :style="{ backgroundImage: background }">Griptape</h1>
					<p>Beautiful dynamic background patterns.</p>
				</div>
			</div>
			<div v-for="(patternConfig, patternKey) in interfaceConfig.patterns" :key="patternKey">
				<pattern-wrapper
					:patternKey="patternKey"
					:label="patternConfig.label"
					:debounce="patternConfig.debounce"
					:config="configs[patternKey]"
					:inputs="patternConfig.inputs"
					:presets="presets[patternKey]"
					@setBackground="key => backgroundPatternKey = key"
					@selectPreset="handleSelectPreset"
					@input="handleInput"
				/>
			</div>
		</div>
	</div>
</template>

<script>
	import 'normalize.css';
	import randomObjKey from 'random-obj-key';
	import griptape from 'griptape';
	import GriptapeInput from './components/GriptapeInput.vue';
	import PatternWrapper from './components/PatternWrapper.vue';

	const inputConfigs = {
		size: {
			common: true,
			inputType: 'range',
			min: 1,
			max: 200,
			step: 1,
		},
		scale: {
			common: true,
			inputType: 'range',
			min: 0.1,
			max: 5,
			step: 0.01,
		},
		foreground: { common: true, inputType: 'color' },
		background: { common: true, inputType: 'color' },
	};

	const configDefaults = {
		size: 20,
		scale: 1,
		foreground: 'rgba(0,0,0,0.05)',
		background: 'rgba(0,0,0,0)',
	};

	export default {
		components: { GriptapeInput, PatternWrapper },
		created() {
			this.backgroundPatternKey = randomObjKey(this.configs);
		},
		data() {
			return {
				backgroundPatternKey: null,
				configs: {
					dots: {
						...configDefaults,
						dotSize: 3,
						pattern: 'diamond',
						shape: 'circle',
					},
					stripes: {
						...configDefaults,
						thickness: 0.5,
						orientation: 'horizontal',
					},
					noise: { ...configDefaults, density: 1 },
					houndstooth: {
						...configDefaults,
						size: 10,
						foreground: 'rgba(122,28,28,1)',
						background: 'rgba(230,230,230,1)',
					},
					grid: {
						...configDefaults,
						shape: 'diamond',
						thickness: 1,
					},
				},
			};
		},
		computed: {
			interfaceConfig() {
				return {
					patterns: {
						dots: {
							label: 'Dots',
							inputs: {
								...inputConfigs,
								dotSize: { inputType: 'range', max: 200 },
								shape: {
									inputType: 'select',
									options: {
										circle: 'Circle',
										square: 'Square',
									},
								},
								pattern: {
									inputType: 'select',
									options: {
										square: 'Square',
										diamond: 'Diamond',
									},
								},
							},
						},
						stripes: {
							label: 'Stripes',
							inputs: {
								...inputConfigs,
								size: { ...inputConfigs.size, min: 2 },
								thickness: {
									inputType: 'range',
									min: 0.1,
									max: 20,
									step: 0.1,
								},
								orientation: {
									inputType: 'select',
									options: {
										horizontal: 'Horizontal',
										vertical: 'Vertical',
									},
								},
							},
						},
						houndstooth: {
							label: 'Houndstooth',
							inputs: { ...inputConfigs },
						},
						grid: {
							label: 'Grid',
							inputs: {
								...inputConfigs,
								shape: {
									inputType: 'select',
									options: {
										square: 'Square',
										diamond: 'Diamond',
									},
								},
								thickness: {
									inputType: 'range',
									min: 0.1,
									max: 20,
									step: 0.1,
								},
							},
						},
						noise: {
							label: 'Noise',
							debounce: 100,
							inputs: {
								...inputConfigs,
								density: {
									inputType: 'range',
									min: 0.1,
									max: 1,
									step: 0.01,
								},
							},
						},
					},
				};
			},
			presets() {
				return {
					dots: {
						subtle: {
							config: {
								size: 3,
								scale: 1,
								foreground: 'rgba(0,0,0,0.30)',
								background: 'rgba(255,255,255,1)',
								dotSize: 2,
								pattern: 'square',
								shape: 'square',
							},
						},
						mint: {
							config: {
								size: 200,
								scale: 1,
								foreground: 'rgba(36,255,245,0.74)',
								background: 'rgba(121,255,138,0.45)',
								dotSize: 50,
								pattern: 'diamond',
								shape: 'circle',
							},
						},
						transparency: {
							config: {
								size: 20,
								scale: 1,
								foreground: 'rgba(0,0,0,0.05)',
								background: 'rgba(255,255,255,1)',
								dotSize: 10,
								pattern: 'diamond',
								shape: 'square',
							},
						},
						mod: {
							config: {
								size: 40,
								scale: 1,
								foreground: 'rgba(0,0,0,1)',
								background: 'rgba(255,255,255,1)',
								dotSize: 20,
								pattern: 'diamond',
								shape: 'square',
							},
						},
					},
					grid: {
						subtle: {
							config: {
								size: 9,
								scale: 1,
								foreground: 'rgba(0,0,0,0.15)',
								background: 'rgba(255,255,255,1)',
								shape: 'diamond',
								thickness: 1,
							},
						},
						graphPaper: {
							config: {
								size: 15,
								scale: 1,
								foreground: 'rgba(0,90,162,0.29)',
								background: 'rgba(94,193,249,0.13)',
								shape: 'square',
								thickness: 1,
							},
						},
					},
					houndstooth: {
						subtle: {
							config: {
								foreground: 'rgba(0,0,0,0.1)',
								background: 'rgba(255,255,255,1)',
							},
						},
						classicBurgundy: {
							config: {
								foreground: 'rgba(122,28,28,1)',
								background: 'rgba(230,230,230,1)',
							},
						},
						classicBlack: {
							config: {
								foreground: 'rgba(0,0,0,1)',
								background: 'rgba(230,230,230,1)',
							},
						},
					},
					stripes: {
						subtle: {
							config: {
								size: 5,
								scale: 1,
								foreground: 'rgba(190,190,190,1)',
								background: 'rgba(255,255,255,1)',
								thickness: 1.0,
								orientation: 'horizontal',
							},
						},
						pinstripes: {
							config: {
								size: 20,
								scale: 1,
								foreground: 'rgba(47,47,47,1)',
								background: 'rgba(25,25,25,1)',
								thickness: 1,
								orientation: 'vertical',
							},
						},
					},
					noise: {
						subtle: {
							config: {
								size: 200,
								scale: 1,
								foreground: 'rgba(0,0,0,0.1)',
								background: 'rgba(255,255,255,1)',
								density: 0.8,
							},
						},
						cork: {
							config: {
								size: 200,
								scale: 1,
								foreground: 'rgba(236,155,53,0.88)',
								background: 'rgba(251,196,114,0.64)',
								density: 0.8,
							},
						},
						static: {
							config: {
								size: 200,
								scale: 1,
								foreground: 'rgba(0,0,0,1)',
								background: 'rgba(255,255,255,1)',
								density: 0.8,
							},
						},
					},
				};
			},
			background() {
				if (this.backgroundPatternKey) {
					return this.getGriptape(this.backgroundPatternKey).toCSSURL();
				}
				return 'transparent';
			},
		},
		methods: {
			getGriptape(key) {
				return griptape[key](this.configs[key]);
			},
			handleInput(pattern, option, value) {
				this.configs[pattern][option] = value;
			},
			handleSelectPreset(patternKey, presetKey) {
				this.configs[patternKey] = {
					...this.configs[patternKey],
					...this.presets[patternKey][presetKey].config,
				};
			},
		},
	};
</script>

<style lang="scss">
	html {
		padding: 0;
		margin: 0;
	}
	body {
		padding: 0;
		margin: 0;
	}
	body, input, button, select {
		font-family: 'Oswald', sans-serif;
	}
	h1, h2, h3, h4, h5, h6 {
		font-family: 'Pirata One', sans-serif;
	}
	pre, input {
		font-family: 'PT Mono', monospace;
	}
	h1, h2, h3, h4, h5, h6 {
		font-weight: normal;
		margin: 0;
	}
	.jumbotron {
		box-shadow: 0px 0px 14px 0px rgba(0,0,0,0.75);
		background: #000;
		padding-top: 5px;
		padding-bottom: 5px;
		color: #fff;
		p {
			margin: 0;
			margin-bottom: 5px;
		}
		h1 {
			background: #fff;
			-webkit-text-fill-color: transparent;
			-webkit-background-clip: text;
			text-shadow: 0px 0 rgba(255,255,255,0.5);
			font-size: 60px;
			color: #fff;
			margin: 0;
		}
	}
	.root {
		background-attachment: fixed;
	}
	.wrap {
		margin: 0 auto;
		max-width: 800px;
		padding-bottom: 100px;
	}
	.contain {
		padding: 0px 20px;
	}
</style>
