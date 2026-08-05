<script lang="ts">
	import { onMount } from 'svelte';

	let video: HTMLVideoElement;
	let canvas: HTMLCanvasElement;

	let stream: MediaStream;

	type Photo = {
		src: string;
		selected: boolean;
	};

	let photos: Photo[] = $state([]);

	let isTakingPhotos = $state(false);

	let buttonSource: string = $state('/record.png');

	let countdown: number = $state(0);

    let selector: HTMLDivElement;

	onMount(async () => {
		stream = await navigator.mediaDevices.getUserMedia({ video: true });
		video.srcObject = stream;
		await video.play();
	});

	function takePhoto() {
		const context = canvas.getContext('2d');

		canvas.width = video.videoWidth;
		canvas.height = video.videoHeight;

		context?.drawImage(video, 0, 0);

		photos = [...photos, { src: canvas.toDataURL('image/png'), selected: false }];
	}

	async function photobooth() {
		isTakingPhotos = true;
		buttonSource = '/disabled.png';

		photos = [];

		try {
			for (let i = 0; i < 6; i++) {
				countdown = 0;

				while (countdown > 0) {
					await new Promise((r) => setTimeout(r, 1000));
					countdown--;
				}

				takePhoto();
				if (i !== 3) await new Promise((r) => setTimeout(r, 500));
			}
		} finally {
			countdown = 0;
			isTakingPhotos = false;
			buttonSource = '/record.png';

			selector?.scrollIntoView({
				behavior: 'smooth',
				block: 'start'
			});
		}
	}
</script>

<main class="flex flex-col items-center justify-center gap-4 p-4">
	<h1>i said hey whats up hello</h1>

	<div class="flex flex-col items-center justify-center gap-2 outline-2">
		{#if countdown !== 0}
			<div class="text-4xl">{countdown}</div>
		{/if}
		<video bind:this={video} autoplay playsinline muted class="scale-x-[-1]"></video>
	</div>

	<button onclick={photobooth} disabled={isTakingPhotos}
		><img src={buttonSource} alt="click to take photos" width="75px" height="75px" /></button
	>

	<canvas bind:this={canvas} class="hidden"> </canvas>

	<div class="flex justify-center">
		<div bind:this={selector} class="flex w-4/5 scale-x-[-1] flex-wrap justify-center gap-2">
			{#each photos as photo}
				<img src={photo.src} alt="" width="400" class="outline" />
			{/each}
		</div>
	</div>
</main>
