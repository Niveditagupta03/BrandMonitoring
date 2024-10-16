<script lang="ts">
	import { goto } from '$app/navigation';
	import type { BrandSentimentResult } from '$lib';
	import { Chart, type ChartTypeRegistry } from 'chart.js/auto';
	import { onMount } from 'svelte';

	let data: { [key: string]: BrandSentimentResult } = {};
	let chartCanvas: HTMLCanvasElement;
	let chart: Chart | null = null;
	let selectedBrands: string[] = [];

	function getBrandData(brands: string[]) {
		if (brands.length === 1) {
			let brandData = data[brands[0]];
			return {
				labels: ['Positive', 'Negative', 'Neutral'],
				datasets: [
					{
						label: 'Sentiment',
						data: [brandData.positive, brandData.negative, brandData.neutral]
					}
				]
			};
		} else if (brands.length > 4) {
			let brandData = Object.entries(data)
				.filter((x) => brands.includes(x[0]))
				.sort((a, b) => b[1].score - a[1].score);
			return {
				labels: brandData.map((brand) => brand[0]),
				datasets: [
					{
						label: 'Overall Score',
						data: brandData.map((brand) => brand[1].score)
					}
				]
			};
		} else if (brands.length > 1) {
			return {
				labels: ['Positive', 'Negative', 'Neutral'],
				datasets: brands.map((x) => {
					let brandData = data[x];
					return {
						label: x,
						data: [brandData.positive, brandData.negative, brandData.neutral]
					};
				})
			};
		} else {
			let brands = Object.entries(data).sort((a, b) => b[1].score - a[1].score);
			return {
				labels: brands.map((brand) => brand[0]),
				datasets: [
					{
						label: 'Overall Score',
						data: brands.map((brand) => brand[1].score)
					}
				]
			};
		}
	}

	function toggleBrand(brand: string | null) {
		if (brand) {
			if (selectedBrands.some((x) => x === brand)) {
				selectedBrands = selectedBrands.filter((x) => x !== brand);
			} else {
				selectedBrands = [...selectedBrands, brand];
			}
		}

		let brandData = getBrandData(selectedBrands);

		let chartType;
		if (selectedBrands.length < 1 || selectedBrands.length > 4) {
			chartType = 'bar';
		} else if (selectedBrands.length === 1) {
			chartType = 'doughnut';
		} else {
			chartType = 'radar';
		}

		if (chart) {
			chart.destroy();
		}

		chart = new Chart(chartCanvas, {
			// @ts-ignore
			type: chartType,
			data: brandData,
			options: { maintainAspectRatio: false }
		});
	}
	onMount(() => {
		let storedData = window.localStorage.getItem('brandData');
		if (storedData) {
			data = JSON.parse(storedData)['data'];
		} else {
			goto('/');
		}
		toggleBrand(null);
	});
</script>

<nav class="flex bg-blue-950">
	<span class="grow"></span>
	<h1 class="mb-4 mt-4 text-5xl text-white">Results</h1>
	<span class="flex grow">
		<span class="grow"></span>
		<button
			class="m-4 inline cursor-pointer rounded-lg bg-blue-900 hover:bg-blue-700"
			on:click={() => goto('/')}
		>
			<p class="m-4 text-xl text-white">Back</p>
		</button></span
	>
</nav>
<div class="m-4 flex grow flex-col">
	<h2 class="mb-4 mt-4 truncate text-5xl text-white">
		{selectedBrands.length > 0
			? selectedBrands.length > 4
				? 'Multiple'
				: selectedBrands.join(' & ')
			: 'Overall'}
	</h2>
	<div class="flex grow rounded-lg bg-blue-950" class:hidden={Object.keys(data).length < 1}>
		<canvas bind:this={chartCanvas} class="m-4"></canvas>
	</div>
</div>
<div class="m-4 flex flex-col">
	<div class="flex">
		<h2 class="mb-4 mt-4 text-5xl text-white">Brands</h2>
		<span class="grow"></span>
		{#if selectedBrands.length > 0}
			<button
				class="m-4 inline cursor-pointer rounded-lg"
				on:click={() => {
					selectedBrands = [];
					toggleBrand(null);
				}}
			>
				<p class="text-xl text-red-600 hover:text-red-500">Clear selection</p>
			</button>
		{/if}
	</div>
	<div class="rounded-lg bg-blue-950">
		<div class="m-4 grid auto-cols-max grid-flow-col grid-rows-2 gap-4 overflow-auto">
			{#each Object.keys(data) as brand}
				<button
					class="inline cursor-pointer rounded-lg hover:bg-blue-700"
					class:bg-blue-800={selectedBrands.includes(brand)}
					class:bg-blue-900={!selectedBrands.includes(brand)}
					on:click={() => toggleBrand(brand)}
				>
					<p class="m-4 text-xl text-white">{brand}</p>
				</button>
			{/each}
		</div>
	</div>
</div>
