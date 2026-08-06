<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.png';
	import Navbar from '$lib/Navbar.svelte';

	let { children } = $props();
</script>

<svelte:head
	><link rel="icon" href={favicon} />
	<title>hc photobooth!</title></svelte:head
>
<Navbar />
{@render children()}
