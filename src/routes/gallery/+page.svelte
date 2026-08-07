<script lang="ts">
	import { onMount, tick } from 'svelte';
	import { Copy, ArrowDownToLine, Trash2 } from 'lucide-svelte';
	import Footer from '$lib/Footer.svelte';

	let gallery: string[] = $state([]);

	let copiedImage = $state<string | null>(null);

	let hoverButton: HTMLAudioElement;
	let clickButton: HTMLAudioElement;

	onMount(() => {
		hoverButton = new Audio('/sounds/hover.mp3');
		clickButton = new Audio('/sounds/click1.mp3');

		const saved = localStorage.getItem('gallery');
		if (saved) {
			gallery = JSON.parse(saved);
		}
	});

	function playHoverButton() {
		if (!hoverButton) return;
		hoverButton.currentTime = 0;
		hoverButton.play();
	}

	function playClickButton() {
		if (!clickButton) return;
		clickButton.currentTime = 0;
		clickButton.play();
	}

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
			copiedImage = image;
			setTimeout(() => {
				if (copiedImage === image) {
					copiedImage = null;
				}
			}, 2000);
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
			<h1 class="text-xl text-dark-red/50 italic">
				huh, looks like your gallery is empty. go <a
					href="/"
					class="font-bold text-red/65 underline hover:decoration-wavy">take some photos!</a
				>
			</h1>
		{/if}
		<div class="mb-8 flex flex-row flex-wrap gap-4">
			{#each gallery as photo}
				<div class="flex flex-col items-center justify-center gap-6">
					{#if copiedImage === photo}
						<div
							class="popup-fadeout absolute -mt-4 rounded-2xl bg-dark-red/75 p-4 font-phantom text-lg text-white"
						>
							copied to clipboard!
						</div>
					{/if}
					<img src={photo} alt="saved photostrip" class="w-2/3 outline outline-dark-red" />
					<div class="flex flex-row gap-4">
						<button
							class="countdown-button"
							onmouseenter={playHoverButton}
							onclick={() => {
								playClickButton();
								copyPhotoStrip(photo);
							}}><Copy color="#57040e" size={15} strokeWidth={2} /></button
						>
						<button
							class="countdown-button"
							onmouseenter={playHoverButton}
							onclick={() => {
								playClickButton();
								downloadPhotoStrip(photo);
							}}><ArrowDownToLine color="#57040e" size={15} strokeWidth={2} /></button
						>
						<button
							class="countdown-button-selected"
							onmouseenter={playHoverButton}
							onclick={() => {
								playClickButton();
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
