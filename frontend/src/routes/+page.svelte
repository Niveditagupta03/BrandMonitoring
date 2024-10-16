<script lang="ts">
	import { goto } from '$app/navigation';
	import { PUBLIC_SENTIMENT_ANALYSIS_BACKEND_URL } from '$env/static/public';

	let form: HTMLFormElement;
	let submitted: boolean = false;

	function submit() {
		submitted = true;
		const response = fetch(`${PUBLIC_SENTIMENT_ANALYSIS_BACKEND_URL}/api/v1/analyze-sentiment/`, {
			method: 'POST',
			body: new FormData(form)
		})
			.then((response) => {
				if (response.ok) {
					return response.json();
				} else {
					throw new Error('Failed to analyze sentiment');
				}
			})
			.then((data) => {
				window.localStorage.setItem('brandData', JSON.stringify(data));
				goto('/results');
			});
	}
</script>

<nav class="flex justify-center bg-blue-950">
	<h1 class="mb-4 mt-4 text-5xl text-white">Brand Sentiment Analysis</h1>
</nav>
<div class="mt-4">
	{#if !submitted}
		<form class="m-4 flex justify-center rounded-lg bg-blue-950" bind:this={form}>
			<input class="m-4 text-white" type="file" name="file" on:change={() => submit()} />
		</form>
	{:else}
		<div class="m-4 flex justify-center rounded-lg bg-blue-950">
			<p class="m-4 text-xl text-white">Loading...</p>
		</div>
	{/if}
</div>
