<template>
	<div class="pattern-wrapper">
		<div class="header">
			<h2>{{ label }}</h2>
		</div>
		<div class="config-panel">
			<div class="content">
				<div v-for="(sectionInputs, sectionKey) in { commonInputs, uniqueInputs }" class="input-section" :class="{ [sectionKey]: true }">
					<griptape-input v-for="(inputConfig, inputKey) in sectionInputs" :key="inputKey"
						:inputType="inputConfig.inputType"
						:pattern="patternKey"
						:option="inputKey"
						:label="inputConfig.label"
						:options="inputConfig.options"
						:value="config[inputKey]"
						:valueType="inputConfig.valueType"
						:min="inputConfig.min"
						:max="inputConfig.max"
						:step="inputConfig.step"
						:debounce="debounce"
						@input="val => $emit('input', patternKey, inputKey, val)"
					/>
				</div>
			</div>
		</div>
		<div v-if="presets" class="presets">
			Presets: <radio-button :options="presetOptions" @click="handlePresetClick" />
		</div>
		<griptape-pattern
			:config="config"
			:patternKey="patternKey"
			:viewingPattern="view === 'pattern'"
		/>
		<div class="footer">
			<g-button style="float: left" @click="$emit('setBackground', patternKey)">Preview</g-button>
			<radio-button
				:options="{ pattern: 'Pattern', config: 'Code' }"
				:value="view"
				@click="handleToggleClick"
			/>
		</div>
	</div>
</template>

<script>
	import randomObjKey from 'random-obj-key';
	import humanizeString from 'humanize-string';
	import GriptapeInput from './GriptapeInput.vue';
	import GriptapePattern from './GriptapePattern.vue';
	import Button from './Button.vue';
	import RadioButton from './RadioButton.vue';

	export default {
		components: {
			gButton: Button,
			RadioButton,
			GriptapeInput,
			GriptapePattern,
		},
		props: {
			label: {},
			patternKey: {},
			config: {},
			inputs: {},
			debounce: {},
			presets: {},
		},
		data() {
			return {
				view: 'pattern',
			};
		},
		mounted() {
			this.$nextTick(() => {
				const presetKey = randomObjKey(this.presets);
				this.$emit('selectPreset', this.patternKey, presetKey);
			});
		},
		computed: {
			commonInputs() {
				return Object.keys(this.inputs).reduce((acc, key) => {
					const input = this.inputs[key];
					if (input.common) {
						acc[key] = input;
					}
					return acc;
				}, {});
			},
			uniqueInputs() {
				return Object.keys(this.inputs).reduce((acc, key) => {
					const input = this.inputs[key];
					if (! input.common) {
						acc[key] = input;
					}
					return acc;
				}, {});
			},
			presetOptions() {
				return this.presets && Object.keys(this.presets).reduce((acc, key) => {
					const preset = this.presets[key];
					acc[key] = preset.label || humanizeString(key);
					return acc;
				}, {});
			},
		},
		methods: {
			handleToggleClick(value) {
				this.view = value;
			},
			handlePresetClick(presetKey) {
				this.$emit('selectPreset', this.patternKey, presetKey);
			},
		},
	};
</script>

<style lang="scss">
	.pattern-wrapper {
		margin: 50px 0px;
		box-shadow: 0px 0px 14px 0px rgba(0,0,0,0.75);
		.header, .footer {
			padding-left: 20px;
			padding-right: 20px;
			background: rgba(0,0,0,0.9);
			h2 {
				font-size: 36px;
				color: #fff;
			}
		}
		.presets {
			color: #fff;
			background: rgba(0,0,0,0.9);
			padding: 10px 20px;
		}
		.header {
			padding-top: 2px;
			padding-bottom: 2px;
		}
		.footer {
			background: rgba(0,0,0,0.8);
			padding-top: 10px;
			padding-bottom: 10px;
			text-align: right;
		}
		@media only screen and (max-width: 768px) {
			margin-left: 20px;
			margin-right: 20px;
		}
	}
	.config-panel {
		background-color: rgba(0,0,0,0.8);
		color: #fff;
		.content {
			padding: 20px;
		}
		.input-section {
			vertical-align: top;
			display: inline-block;
			width: 50%;
			@media only screen and (max-width: 768px) {
				width: 100%;
			}
		}
	}
</style>
