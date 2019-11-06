<template>
	<div
		id="custom-select"
		class="flex flex-row w-full items-center rounded-t mb-6 px-1 border-b focus-within:bg-gray-200"
		v-bind:class="{
			'border-blue-400': active && inputError.length < 1,
			'border-red-600': active && inputError.length > 0
		}"
	>
		<slot name="leading-icon"></slot>
		<div class="relative flex flex-col w-full">
			<div class="relative flex flex-col p-1">
				<label
					for="input"
					class="this-select-label absolute text-sm z-10 text-gray-500"
					v-bind:class="activeLabel"
					>{{ label }}{{ hasError }}</label
				>
				<div class="flex flex-row w-full">
					<select
						v-bind:id="name"
						v-bind:name="name"
						:required="required"
						class="this-input bg-transparent focus:outline-none mt-6 z-20 w-full"
						v-bind:class="{ 'text-red-600': inputError.length > 0 }"
						@focus="active = true"
						@focusout="validateOnBlur($event)"
						@keypress="inputError = ''"
						v-bind:value="value"
						v-on:input="$emit('input', $event.target.value)"
					>
						<slot></slot>
					</select>
				</div>
			</div>
			<transition name="fade-left">
				<div
					v-if="active && inputError.length < 1"
					class="this-helper absolute bottom-0 -mb-5 text-gray-600 text-xs md:text-sm font-thin p-1 flex justify-end w-full"
				>
					<slot name="helper"></slot>
				</div>
			</transition>
			<transition name="fade-right">
				<div
					v-if="inputError.length > 0"
					class="this-error absolute bottom-0 -mb-5 text-red-600 text-xs font-thin p-1"
				>
					{{ inputError }}
				</div>
			</transition>
		</div>
		<slot name="trailing-icon"></slot>
	</div>
</template>
<script>
export default {
	data() {
		return {
			active: false,
			hasInput: false,
			inputError: ""
		};
	},
	computed: {
		activeLabel() {
			return {
				"text-xs font-semibold": this.active,
				"text-red-600": this.inputError.length > 0,
				"text-blue-500": this.inputError.length < 1 && this.active,
				"text-gray-500": this.inputError.length < 1 && !this.active
			};
		},
		hasError() {
			return this.inputError.length > 0 ? "*" : "";
		}
	},
	watch: {
		error() {
			this.inputError = this.error ? this.error[0] : "";
		},
		value() {
			if (this.value.length > 0) this.hasInput = true;
			else this.hasInput = false;
		}
	},
	methods: {
		validateOnBlur(event) {
			this.active = false;
			if (this.inputError.length > 0) {
				this.inputError = "";
			}
		}
	},
	props: {
		name: {
			type: String,
			default() {
				return Math.random()
					.toString(36)
					.substr(2);
			}
		},
		label: {
			type: String,
			default() {
				return "Input Label";
			}
		},
		required: String,
		error: Array,
		value: String | Number
	}
};
</script>
<style>
#custom-select {
	transition: background-color 0.3s ease-out;
}
.this-select-label {
	transition: color 0.5s ease-out;
	transition: font-weight 0.2s ease;
}
</style>
