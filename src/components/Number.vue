<script setup lang="ts">
// Props and emits are defined here.
defineProps({
	number: {
		type: Number,
		required: true,
	},
	active: {
		type: Boolean,
		default: false,
	},
});

const emit = defineEmits(['hover', 'reset']);
</script>

<template>
	<!--
		- @mouseover and @mouseout events are used to emit the hover and reset events to the parent element (Numbers).
	-->
	<div
		class="number"
		:class="{ active: active }"
		@mouseover="emit('hover', number)"
		@mouseout="emit('reset')"
	>
		{{ number }}
	</div>
</template>

<style>
.number {
	display: inline-block;
	padding: 5px;
	background-color: black;
	margin: 5px;
	transition: 0.4s;
}

.number:hover {
	background-color: #1529bd;
	transform: translateY(-0.5rem) scale(1.2);
}

.active {
	background-color: #668ce4;
}
</style>
