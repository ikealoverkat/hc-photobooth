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
	let frameSelector: HTMLDivElement;
    let printSection: HTMLDivElement;

	let nextButton: HTMLButtonElement;

	let printCanvas: HTMLCanvasElement;    

	let frames: Frame[] = $state([
		{ src: '/frame_hc.png', name: 'hack club themed frame!', selected: false },
		{ src: '/frame_hctg.png', name: 'hack club the frame', selected: false },
		{ src: '/frame_intern.png', name: 'summer internship frame!', selected: false },
		{ src: '/frame_ovg.png', name: 'overglade frame. senator im singaporean', selected: false },
		{ src: '/frame_ysws.png', name: 'ysws frame! dont we all love free stuff?', selected: false }
	]);

	onMount(async () => {
        printCanvas = document.createElement("canvas");

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

	function toggleFrameSelect(selectedFrame: Frame) {
		for (const frame of frames) {
			frame.selected = frame === selectedFrame;
		}

		frames = [...frames];

		if (nextButton.classList.contains('hidden')) {
			nextButton.classList.remove('hidden');
			nextButton.classList.add('flex');
		}
	}

	async function loadImage(src: string): Promise<HTMLImageElement> {
		const img = new Image();
		img.src = src;

		await img.decode();

		return img;
	}

	async function makePhotoStrip() {
		printCanvas.classList.remove('hidden');

        const selectedPhotos = photos.filter((photo) => photo.selected);
		const selectedFrame = frames.find((frame) => frame.selected);

		if (!selectedFrame) return;

		const frame = await loadImage(selectedFrame.src);

		const images = await Promise.all(selectedPhotos.map((photo) => loadImage(photo.src)));

		console.log(selectedPhotos);
		console.log(selectedFrame);

		const context = printCanvas.getContext('2d');
		printCanvas.width = 200;
		printCanvas.height = 750;

		context?.drawImage(images[0], 0, 50, 230, 140);
		context?.drawImage(images[1], 0, 210, 230, 140);
		context?.drawImage(images[2], 0, 370, 230, 140);
		context?.drawImage(images[3], 0, 530, 230, 140);

		context?.drawImage(frame, 0, 0, 200, 750);
	}
</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	<!-- photo taking part -->
	<div class="flex h-screen flex-col items-center justify-center">
		<div
			class="flex flex-col items-center justify-center gap-4 bg-red/10 p-24 shadow-md shadow-red/35 outline-2 outline-red/35 backdrop-blur-[2.5px] duration-200 hover:scale-101"
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
					class="max-w-200 scale-x-[-1] outline-2 outline-dark-red"
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
		<button
			class="red-button bright-red-shadow mt-2"
			onclick={() => {
				frameSelector.classList.remove('hidden');
				frameSelector.classList.add('flex');
				frameSelector.scrollIntoView({
					behavior: 'smooth',
					block: 'start'
				});
			}}>next!</button
		>
	</div>

	<!-- choose frame -->
	<div bind:this={frameSelector} class="hidden h-screen flex-col items-center gap-4">
		<h1 class="font-phantom text-4xl font-bold text-dark-red">choose a photo frame!</h1>
		<div class="flex flex-row overflow-x-scroll p-8">
			{#each frames as frame, i}
				<!-- svelte-ignore a11y_click_events_have_key_events -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<div class={i === 0 ? 'group' : 'group -ml-12'}>
					<img
						src={frame.src}
						alt={frame.name}
						class={frame.selected
							? 'relative z-50 w-50 outline-4 outline-red transition-all duration-500 hover:mx-16 hover:scale-103'
							: 'relative w-50 outline transition-all duration-500 hover:mx-16 hover:scale-103'}
						onclick={() => {
							toggleFrameSelect(frame);
						}}
					/>
					<h2
						class="font-italic m-4 max-w-40 justify-self-center text-center font-phantom text-xl text-wrap text-dark-red opacity-0 duration-500 group-hover:opacity-100"
					>
						{frame.name}
					</h2>
				</div>
			{/each}
		</div>
		<button
			class="red-button bright-red-shadow -mt-14 hidden w-fit self-center"
			bind:this={nextButton}
			onclick={() => {
				printSection.scrollIntoView({
					behavior: 'smooth',
					block: 'start'
				});
			}}>next!</button
		>
	</div>
	<!-- "print" photostrip -->
	<div bind:this={printSection} class="flex flex-col items-center justify-center gap-4">
		<h1 class="font-phantom text-4xl font-bold text-dark-red">print your photo strip</h1>    
		<button class="red-button bright-red-shadow" onclick={makePhotoStrip}>print!</button>
        <hr class="w-[50%] self-center border-2 border-dark-red/50 -mb-4" />
		<canvas bind:this={printCanvas} class="hidden outline outline-dark-red"></canvas>
	</div>
</main>
