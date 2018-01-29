<template>
	<span class="radio">
		<g-button
			v-for="(label, loopValue, i) in options"
			:key="loopValue"
			:class="getOptionClasses(loopValue, i)"
			@click="handleClick(loopValue)"
		>{{ label }}</g-button>
	</span>
</template>

<script>
	import Button from './Button.vue';

	export default {
		components: { gButton: Button },
		props: {
			options: {},
			value: {},
		},
		methods: {
			handleClick(value) {
				if (this.value !== value) {
					this.$emit('change', value);
					this.$emit('input', value);
				}
				this.$emit('click', value);
			},
			getOptionClasses(loopValue, i) {
				const classes = {
					'radio-button': true,
					'radio-active': loopValue === this.value,
				};
				if (Object.keys(this.options).length === 1) {
					classes['radio-single'] = true;
				} else {
					classes['radio-first'] = i === 0;
					classes['radio-last'] = i === Object.keys(this.options).length - 1;
					classes['radio-middle'] = ! classes['radio-first'] && ! classes['radio-last'];
				}
				return classes;
			},
		},
	};
</script>

<style lang="scss" scoped>
	.radio {
		position: relative;
		top: 1px;
	}
	.button {
		&.radio-button {
			position: relative;
			bottom: 1px;
			&:focus {
				outline: none;
			}
		}

		&.radio-first {
			border-top-right-radius: 0;
			border-bottom-right-radius: 0;
			border-right: none;
			border-right: 1px solid #000;
		}
		&.radio-middle {
			border-radius: 0;
			border-left: none;
			border-right: none;
			border-right: 1px solid #000;
		}
		&.radio-last {
			border-top-left-radius: 0;
			border-bottom-left-radius: 0;
			border-left: none;
		}
		&.radio-active {
			background-color: rgba(50,50,50,0.8);
			border-top: 2px solid #000;
			bottom: 0px;
			padding-bottom: 4px;
			color: #eee;
			&:hover {
				background-color: rgba(50,50,50,0.8);
			}
		}
	}
</style>
