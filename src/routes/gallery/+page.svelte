<script lang="ts">
	import { onMount } from 'svelte';

	let gallery: string[] = $state([]);

	onMount(() => {
		const saved = localStorage.getItem('gallery');
		if (saved) {
			gallery = JSON.parse(saved);
		}
	});
</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	<h1>gallery woah</h1>
	{#each gallery as photo}
		<img src={photo} alt="saved photostrip" class="outline outline-dark-red" />
	{/each}
</main>
