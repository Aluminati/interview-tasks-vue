<script setup lang="ts">
/*
OVERALL CHANGES:
	- Using meaningful variable names.
	- Using reactive values to work with the data.
	- Creating a new component (Numbers.vue) to improve the modularity of the application.
	- Improve application performance by caching the divisors of a number.
 */
import { ref, computed, watch } from 'vue';
import Number from './Number.vue';

const limit = ref(100);
const hoveredNumber = ref<number | null>(null);
const numbers = ref<number[]>([]);
const divisorsCache = new Map<number, number[]>(); // Cache divisors already seen

// Fisher-Yates shuffle algorithm to randomize the numbers (source: gpt-o3-mini-high).
// Performance improved from O(n log n) to O(n).
function sortNumbers() {
	const numbersArray = Array.from({ length: limit.value - 1 }, (_, i) => i + 1);

	for (let i = numbersArray.length - 1; i > 0; i--) {
		const j = Math.floor(Math.random() * (i + 1));
		[numbersArray[i], numbersArray[j]] = [numbersArray[j], numbersArray[i]];
	}

	numbers.value = numbersArray;
}

// Regenerates the array of numbers when the limit changes (and when the app is created).
watch(limit, sortNumbers, { immediate: true });

// Used to calculate and show the divisors of the hovered number.
const divisors = computed(() => {
	if (!hoveredNumber.value) return []; // No number hovered === no divisors

	const currentHoveredNumber = hoveredNumber.value;

	if (divisorsCache.has(currentHoveredNumber)) return divisorsCache.get(currentHoveredNumber); // Return cached divisors if available

	const result = [];

	// Iterate from 1 to the current hovered number and checks if the number divides evenly.
	for (let i = 1; i < currentHoveredNumber; i++) {
		if (currentHoveredNumber % i === 0) {
			result.push(i);
		}
	}

	divisorsCache.set(currentHoveredNumber, result);

	return result;
});
</script>

<template>
	<!--
		- Removed <br /> tags to add proper spacing.
		- Added a div to wrap the numbers.
		- Added the Number component for each number in the array.
		- For each number in the numbers array, a component Number is rendered.
			- The Number Component receives the number and a boolean to check if it's active.
			- The events @hover and @reset events are emitted in the Number component when the mouse is over (or out of) the number.
			- @hover: updates the hoveredNumber value, which triggers the divisors computed property.
			- @reset: resets the hoveredNumber value to null, which also triggers the divisors computed property.
	-->
	<main class="container">
		<input type="number" v-model.number="limit" class="number_input" />
		<div class="numbers_wrapper">
			<Number
				v-for="number in numbers"
				:key="number"
				:number="number"
				:active="divisors.includes(number)"
				@hover="(number) => (hoveredNumber = number)"
				@reset="() => (hoveredNumber = null)"
			/>
		</div>
	</main>
</template>

<style scoped>
/*
	- Number styles removed from here.
	- Added a container class to center the content.
	- Added a numbers_wrapper class to display the numbers in a responsive grid.
	- Handles the number_input style.
 */

.container {
	display: flex;
	flex-direction: column;
	min-height: 100vh;
	width: 100%;
}

.numbers_wrapper {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(50px, 1fr));
}

.number_input {
	padding: 0.5rem;
	max-width: 200px;
	align-self: center;
	margin: 1rem;
}
</style>
