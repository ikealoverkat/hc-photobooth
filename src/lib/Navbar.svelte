<script lang="ts">
	import { onMount } from 'svelte';

	let hoverButton: HTMLAudioElement;
	let clickButton: HTMLAudioElement;

	onMount(() => {
		hoverButton = new Audio('/sounds/hover2.wav');
		clickButton = new Audio('/sounds/click2.mp3');
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
</script>

<div
	class="dots-bg sticky top-0 z-20 mx-auto flex flex-row justify-center gap-4 border-b-2 border-dark-red/10 p-4 px-8 pt-4 shadow-md shadow-dark-red/5"
>
	<a
		href="/"
		class="text-xl font-bold text-dark-red/75 transition-all duration-200 hover:scale-105 hover:px-2"
		onmouseenter={playHoverButton}
		onclick={playClickButton}>photobooth</a
	>
	<a
		href="/gallery"
		class="px-4 text-xl font-bold text-dark-red/75 transition-all duration-200 hover:scale-105 hover:px-6 hover:text-dark-red"
		onmouseenter={playHoverButton}
		onclick={playClickButton}>your gallery</a
	>
	<a
		href="/frames"
		class="text-xl font-bold text-dark-red/75 transition-all duration-200 hover:scale-105 hover:px-2"
		onmouseenter={playHoverButton}
		onclick={playClickButton}>see all frames</a
	>
</div>
