<script>
	import '../app.css';
</script>

<div class="flex h-screen max-h-screen flex-col overflow-hidden">
	<slot></slot>
</div>

<style>
	:global(body) {
		background-color: #0f1029;
	}

	:global(*) {
		font-family: 'Lato', sans-serif;
		font-weight: 400;
		font-style: normal;
	}

	:global(::-webkit-scrollbar) {
		width: 20px;
	}

	:global(::-webkit-scrollbar-track) {
		background-color: transparent;
	}

	:global(::-webkit-scrollbar-thumb) {
		background-color: #d6dee1;
		border-radius: 20px;
		border: 6px solid transparent;
		background-clip: content-box;
	}

	:global(::-webkit-scrollbar-thumb:hover) {
		background-color: #a8bbbf;
	}
</style>
