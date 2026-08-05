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

    function togglePhotoSelect(photo: Photo) {
        const selectedCount = photos.filter(p => p.selected).length;
        if (!photo.selected && selectedCount >= 4) {
            return;
        }

        photo.selected = !photo.selected;
        photos = [...photos];
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
            
            selector.classList.remove('hidden');
            selector.classList.add('flex');

			selector?.scrollIntoView({
				behavior: 'smooth',
				block: 'start'
			});
		}
	}
</script>

<main class="flex flex-col items-center justify-center gap-4 p-4">
	<!-- photo taking part -->
	<div class="h-screen flex flex-col items-center justify-center gap-4 -mt-4">
		<div class="text-center">
			<h1 class="font-phantom text-4xl font-bold">hack club photobooth!</h1>
			<p class="font-phantom text-2xl">
				it's just a normal photobooth, but all the frames are hack club themed :p
			</p>
		</div>

		<div class="flex flex-col items-center justify-center gap-2">
			{#if countdown !== 0}
				<div class="text-4xl">{countdown}</div>
			{/if}
			<video bind:this={video} autoplay playsinline muted class="scale-x-[-1] outline-2"></video>
		</div>

		<button onclick={photobooth} disabled={isTakingPhotos}
			><img src={buttonSource} alt="click to take photos" width="75px" height="75px" /></button
		>
	</div>
	<canvas bind:this={canvas} class="hidden"> </canvas>

	<div bind:this={selector} class="hidden flex-col gap-4 h-screen items-center justify-center">
        <h1 class="font-phantom text-4xl font-bold m-4">choose four pictures to keep</h1>
		<div class="flex max-h-fit w-4/5 scale-x-[-1] flex-wrap justify-center self-center gap-4 duration-200">
			{#each photos as photo}
				<!-- svelte-ignore a11y_click_events_have_key_events -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<img src={photo.src} alt="" width="400" class={photo.selected ? 'outline-4 outline-blue-500 hover:scale-103 active:scale-102 duration-200' : 'outline duration-200 hover:scale-103'} onclick={() => togglePhotoSelect(photo)} />
			{/each}
		</div>
	</div>
</main>
