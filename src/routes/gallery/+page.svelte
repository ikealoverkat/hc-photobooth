<script lang="ts">
	import { onMount } from 'svelte';
	import { Copy, ArrowDownToLine, Trash2 } from 'lucide-svelte';
	import Footer from '$lib/Footer.svelte';

	let gallery: string[] = $state([]);

	onMount(() => {
		const saved = localStorage.getItem('gallery');
		if (saved) {
			gallery = JSON.parse(saved);
		}
	});

	function downloadPhotoStrip(image: string) {
		const link = document.createElement('a');
		link.href = image;
		link.download = 'photostrip.png';
		link.click();
	}

	async function copyPhotoStrip(image: string) {
		try {
			const response = await fetch(image);
			const blob = await response.blob();

			await navigator.clipboard.write([
				new ClipboardItem({
					'image/png': blob
				})
			]);

			console.log('image copied to clipboard');
		} catch (error: any) {
			console.error('failed to copy image: ', error.name, error.message);
		}
	}

	function deleteFromStorage(image: string) {
		gallery = gallery.filter((p) => p !== image);
		localStorage.setItem('gallery', JSON.stringify(gallery));
	}
</script>

<main class="dots-bg flex min-h-screen flex-col items-center justify-center gap-4 p-4">
	<h1 class="m-4 text-4xl font-bold text-red">gallery</h1>
	<p class="-mt-6 mb-6 text-xl text-dark-red">
		these pictures are stored in localstorage. don't worry, i don't have access to them :p
	</p>
	<div class="flex flex-col gap-4">
		{#if gallery.length == 0}
			<h1 class="text-xl text-dark-red/50 italic">huh, looks like your gallery is empty. go <a href="/" class="text-red/65 font-bold underline hover:decoration-wavy">take some photos!</a></h1>
		{/if}
		<div class="flex flex-row flex-wrap gap-4">
			{#each gallery as photo}
				<div class="flex flex-col items-center justify-center gap-6">
					<img src={photo} alt="saved photostrip" class="w-2/3 outline outline-dark-red" />
					<div class="flex flex-row gap-4">
						<button
							class="countdown-button"
							onclick={() => {
								copyPhotoStrip(photo);
							}}><Copy color="#57040e" size={15} strokeWidth={2} /></button
						>
						<button
							class="countdown-button"
							onclick={() => {
								downloadPhotoStrip(photo);
							}}><ArrowDownToLine color="#57040e" size={15} strokeWidth={2} /></button
						>
						<button
							class="countdown-button-selected"
							onclick={() => {
								deleteFromStorage(photo);
							}}><Trash2 color="#57040e" size={15} strokeWidth={2} /></button
						>
					</div>
				</div>
			{/each}
		</div>

		<Footer />
	</div>
</main>
