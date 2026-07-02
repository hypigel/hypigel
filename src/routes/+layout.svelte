<script lang="ts">
	import favicon from '$lib/assets/favicon.png';
	import '../routes/app.css';
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
