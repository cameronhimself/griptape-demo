<template>
	<div class="input-container">
		<label :for="name" class="label">{{ computedLabel }} </label>
		<vue-slider v-if="inputType === 'range'"
			:min="min || 1"
			:max="max || 200"
			:interval="step || 1"
			:id="htmlId"
			:name="name"
			:value="value"
			:width="'50%'"
			:process-style="{ backgroundColor: 'rgba(80,80,80,0.8)' }"
			:tooltip-style="{ display: 'none' }"
			@input="handleRangeInput"
		/>
		<div class="color-picker-wrap" v-else-if="inputType === 'color'">
			<div class="swatch-container">
				<div
					class="swatch"
					:style="{ backgroundColor: colorString }"
					@click="open = ! open"
				></div>
			</div>
			<div v-if="open" v-on-clickaway="() => open = false" class="color-picker" @click.stop>
				<color-picker 
					:value="this.color"
					@input="handleColorInput"
				/>
			</div>
		</div>
		<radio-button v-else-if="inputType === 'select'"
			:id="htmlId"
			:name="name"
			:options="options"
			:value="value"
			@input="handleSelectInput"
		/>
	</div>
</template>

<script>
	import debounce from 'debounce';
	import colorString from 'color-string';
	import humanizeString from 'humanize-string';
	import VueSlider from 'vue-slider-component';
	import VueClickaway from 'vue-clickaway';
	import { Chrome } from 'vue-color';
	import RadioButton from './RadioButton.vue';

	function maybeDebounce(func, debounceMs) {
		if (debounceMs) {
			return debounce(func, debounceMs);
		}
		return func;
	}

	export default {
		components: { colorPicker: Chrome, VueSlider, RadioButton },
		mixins: [VueClickaway.mixin],
		data() {
			return {
				open: false,
				color: {
					r: 0,
					g: 0,
					b: 0,
					a: 0,
				},
			};
		},
		props: {
			pattern: {},
			option: {},
			value: {},
			inputType: {},
			label: {},
			min: {},
			max: {},
			step: {},
			options: {},
			debounce: { required: false, default: 0 },
		},
		created() {
			if (this.inputType === 'color') {
				const parsed = colorString.get.rgb(this.value);
				this.color = {
					r: parsed[0],
					g: parsed[1],
					b: parsed[2],
					a: parsed[3],
				};
			}
		},
		computed: {
			htmlId() {
				return `${this.pattern}_${this.option}`;
			},
			name() {
				return this.htmlId;
			},
			computedLabel() {
				return this.label || humanizeString(this.option);
			},
			colorString() {
				let rgba;
				if (this.color.r !== undefined) {
					rgba = this.color;
				}
				if (this.color.rgba) {
					rgba = this.color.rgba;
				}
				return `rgba(${rgba.r},${rgba.g},${rgba.b},${rgba.a})`;
			},
			handleInput() {
				return maybeDebounce(this._handleInput, this.debounce);
			},
			handleRangeInput() {
				return maybeDebounce(this._handleRangeInput, this.debounce);
			},
			handleSelectInput() {
				return maybeDebounce(this._handleSelectInput, this.debounce);
			},
			handleColorInput() {
				return maybeDebounce(this._handleColorInput, this.debounce);
			},
		},
		methods: {
			debounced(func) {
				if (this.debounce) {
					return debounce(func, this.debounce);
				}
				return func;
			},
			_handleInput(event) {
				let value = event.target.value;
				if (this.inputType === 'range') {
					value = Number(value);
				}
				this.$emit('input', value);
			},
			_handleRangeInput(value) {
				this.$emit('input', value);
			},
			_handleSelectInput(value) {
				this.$emit('input', value);
			},
			_handleColorInput(value) {
				this.color = value;
				this.$emit('input', this.colorString);
			},
		},
		watch: {
			value() {
				if (this.inputType === 'color') {
					const parsed = colorString.get.rgb(this.value);
					this.color = {
						r: parsed[0],
						g: parsed[1],
						b: parsed[2],
						a: parsed[3],
					};
				}
			},
		},
	};
</script>

<style lang="scss">
	.vue-slider-component {
		display: inline-block;
	}
	.vue-slider {
		margin-left: -8px;
	}
	.input-container {
		margin-bottom: 8px;
		> * {
			vertical-align: middle;
		}
	}
	.label {
		display: inline-block;
		width: 80px;
	}
	.color-picker-wrap {
		display: inline-block;
		position: relative;
	}
	.color-picker {
		position: absolute;
		z-index: 10;
	}
	.swatch-container {
		background-color: #fff;
		border: 1px solid #000;
		width: 16px;
		height: 16px;
	}
	.swatch {
		width: 100%;
		height: 100%;
	}
</style>
