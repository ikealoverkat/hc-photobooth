<script lang="ts">
	import { onMount } from 'svelte';

	let video: HTMLVideoElement;
	let canvas: HTMLCanvasElement;

	let stream: MediaStream;

	type Photo = {
		src: string;
		selected: boolean;
	};

	type Frame = {
		src: string;
		name: string;
		selected: boolean;
	};

	let photos: Photo[] = $state([]);

	let isTakingPhotos = $state(false);

	let buttonSource: string = $state('/record.png');

	let countdown: number = $state(0);

	let selector: HTMLDivElement;

	let frames: Frame[] = [];
	// import this once i actually have frames

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
		const selectedCount = photos.filter((p) => p.selected).length;
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

	function toggleFrameSelect(frame: Frame) {
		const selectedCount = photos.filter((f) => f.selected).length;
		if (!frame.selected && selectedCount >= 1) {
			return;
		}
        frame.selected = !frame.selected;
	}
</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	<!-- photo taking part -->
	<div class="flex h-screen flex-col items-center justify-center duration-200 hover:scale-101">
		<div
			class="flex flex-col items-center justify-center gap-4 bg-red/10 p-24 shadow-md shadow-red/35 outline-2 outline-red/35 backdrop-blur-[2.5px]"
		>
			<div class="mb-8 text-center">
				<h1 class="font-phantom text-4xl font-bold text-dark-red">hack club photobooth!</h1>
				<p class="font-phantom text-2xl text-dark-red/75">
					it's just a normal photobooth, but all the frames are hack club themed :p
				</p>
			</div>

			<div class="flex flex-col items-center justify-center gap-2">
				{#if countdown !== 0}
					<div class="font-phantom text-4xl text-dark-red">{countdown}</div>
				{/if}
				<video
					bind:this={video}
					autoplay
					playsinline
					muted
					class="scale-x-[-1] outline-2 outline-dark-red"
				></video>
			</div>

			<button onclick={photobooth} disabled={isTakingPhotos}
				><img
					src={buttonSource}
					alt="click to take photos"
					width="75px"
					height="75px"
					class="button-shadow opacity-50 duration-200 hover:scale-108 hover:opacity-75"
				/></button
			>
		</div>
	</div>
	<canvas bind:this={canvas} class="hidden"> </canvas>

	<!-- select photos -->
	<div bind:this={selector} class="hidden h-screen flex-col items-center justify-center gap-4">
		<h1 class="m-4 font-phantom text-4xl font-bold text-dark-red">choose four pictures to keep</h1>
		<div
			class="flex max-h-fit w-4/5 scale-x-[-1] flex-wrap justify-center gap-4 self-center duration-200"
		>
			{#each photos as photo}
				<!-- svelte-ignore a11y_click_events_have_key_events -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<img
					src={photo.src}
					alt=""
					width="400"
					class={photo.selected
						? 'outline-4 outline-red duration-200 hover:scale-102 active:scale-101'
						: 'outline duration-200 hover:scale-103'}
					onclick={() => togglePhotoSelect(photo)}
				/>
			{/each}
		</div>
		<button class="red-button bright-red-shadow">next!</button>
	</div>

	<!-- choose frame -->
	<div class="flex flex-col h-screen gap-4">
		<h1 class="text-4xl font-phantom text-dark-red font-bold">choose a photo frame!</h1>
		<div class="flex flex-row overflow-x-scroll -px-16">
			{#each frames as frame}
				<!-- svelte-ignore a11y_click_events_have_key_events -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<img
					src={frame.src}
					alt={frame.name}
					class={frame.selected
						? 'outline-4 outline-red duration-200 hover:scale-102 hover:z-100 active:scale-101'
						: 'outline duration-200 hover:scale-103'}
					onclick={() => toggleFrameSelect(frame)}
				/>
			{/each}
		</div>
	</div>
	<!-- "print" photostrip -->
	<div></div>
</main>
