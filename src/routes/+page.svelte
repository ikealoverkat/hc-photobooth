<script lang="ts">
	import { onMount, tick } from 'svelte';

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
	let countdownTime: number = $state(3);
	let countdownOptions = [0, 1, 3, 5];


	let flash = $state(false);

	let selector: HTMLDivElement;
	let frameSelector: HTMLDivElement;
	let printSection: HTMLDivElement;

	let nextButton: HTMLButtonElement;

	let printCanvas: HTMLCanvasElement;
	let printed = $state(false);

	let frames: Frame[] = $state([
		{ src: '/frame_hc.png', name: 'hack club themed frame!', selected: false },
		{ src: '/frame_hctg.png', name: 'hack club the frame', selected: false },
		{ src: '/frame_intern.png', name: 'summer internship frame!', selected: false },
		{ src: '/frame_ovg.png', name: 'overglade frame. senator im singaporean', selected: false },
		{ src: '/frame_ysws.png', name: 'ysws frame! dont we all love free stuff?', selected: false }
	]);

	onMount(async () => {
		printCanvas = document.createElement('canvas');

		try {
			stream = await navigator.mediaDevices.getUserMedia({
				video: true
			});
		} catch (e) {
			console.error(e);
			alert(e instanceof Error ? e.message : String(e));
		}

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

	async function cameraFlash() {
		flash = true;
		await new Promise((r) => setTimeout(r, 100));
		flash = false;
	}

	async function photobooth() {
		isTakingPhotos = true;
		buttonSource = '/disabled.png';

		photos = [];

		try {
			for (let i = 0; i < 6; i++) {
				countdown = countdownTime;

				while (countdown > 0) {
					await new Promise((r) => setTimeout(r, 1000));
					countdown--;
				}
				await cameraFlash();
				await tick();
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

	function downloadPhotoStrip() {
		if (!printCanvas) return;

		const image = printCanvas.toDataURL('image/png');
		const link = document.createElement('a');
		link.href = image;
		link.download = 'photostrip.png';
		link.click();
	}

	async function copyPhotoStrip() {
		if (!printCanvas) return;

		const blob = await new Promise<Blob | null>((resolve) =>
			printCanvas.toBlob(resolve, 'image/png')
		);

		if (!blob) return;

		await navigator.clipboard.write([
			new ClipboardItem({
				'image/png': blob
			})
		]);

		console.log('copied to clipboard!');
	}
</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	{#if flash}
		<div class="animate-flash pointer-events-none fixed inset-0 z-20 bg-white"></div>
	{/if}
	<!-- photo taking part -->
	<div class="scroll-appear flex h-screen flex-col items-center justify-center">
		<div
			class="flex flex-col items-center justify-center gap-4 rounded-xl bg-red/10 p-24 shadow-md shadow-red/35 outline-2 outline-red/35 backdrop-blur-[2.5px] duration-200 hover:scale-101"
		>
			<div class="text-center">
				<h1 class="font-phantom text-4xl font-bold text-dark-red">hack club photobooth!</h1>
				<p class="font-phantom text-2xl text-dark-red/75">
					it's just a normal photobooth, but all the frames are hack club themed :p
					<br />take pictures w/your friends @ HC events! ^_^ or by yourself. that works too.
				</p>
			</div>

			<div class="m-4 flex flex-row items-center gap-4">
				<h2 class="font-phantom text-lg font-bold text-dark-red/50">COUNTDOWN</h2>
				{#each countdownOptions as time}
					<button
						class={`countdown-button ${
							countdownTime === time ? 'countdown-button-selected' : 'countdown-button-hover'
						}`}
						onclick={() => (countdownTime = time)}
					>
						{time === 0 ? 'burst (0)' : `${time}s`}
					</button>
				{/each}
			</div>

			<div class="flex flex-col items-center justify-center gap-2">
				{#if countdown !== 0}
					<div class="my-4 font-phantom text-6xl text-dark-red/75">{countdown}</div>
				{/if}
				<div>
					<video
						bind:this={video}
						autoplay
						playsinline
						muted
						class="max-w-200 scale-x-[-1] outline-2 outline-dark-red"
					>
					</video>
				</div>
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
	<div
		bind:this={selector}
		class="scroll-appear hidden h-screen flex-col items-center justify-center gap-4"
	>
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
	<div bind:this={frameSelector} class="scroll-appear hidden h-screen flex-col items-center gap-4">
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
				printSection.classList.remove('hidden');
				printSection.classList.add('flex');
				printSection.scrollIntoView({
					behavior: 'smooth',
					block: 'start'
				});
			}}>next!</button
		>
	</div>
	<!-- "print" photostrip -->
	<div
		bind:this={printSection}
		class="scroll-appear hidden h-screen flex-col items-center justify-center gap-4"
	>
		<h1 class="font-phantom text-4xl font-bold text-dark-red">print your photo strip</h1>
		<button
			class="red-button bright-red-shadow"
			onclick={async (e) => {
				printed = true;
				await tick();
				makePhotoStrip();
				(e.currentTarget as HTMLButtonElement).classList.add('hidden');
			}}>print!</button
		>
		<div class="flex flex-row items-center justify-center gap-32">
			{#if printed}
				<div class="printer relative">
					<hr class="absolute top-0 left-0 z-20 -mb-4 w-full border-2 border-dark-red/50" />
					<canvas bind:this={printCanvas} class="hidden"></canvas>
				</div>

				<div class="print-fadein flex flex-col gap-2 opacity-0">
					<button class="red-button bright-red-shadow" onclick={downloadPhotoStrip}
						>download as png</button
					>
					<button class="red-button bright-red-shadow" onclick={copyPhotoStrip}
						>copy to clipboard</button
					>
					<button
						class="red-button bright-red-shadow font-bold"
						onclick={() => {
							location.reload();
						}}>again!</button
					>
				</div>
			{/if}
		</div>
	</div>
	<div class="p-4 pb-16 text-center">
		<h1 class="font-phantom text-2xl text-dark-red">
			made with <b class="text-red">♡</b> by
			<a
				href="https://kat.wang"
				target="_blank"
				class="text-red italic underline hover:decoration-wavy">kat wang</a
			>
		</h1>
		<h2 class="m-2 font-phantom text-xl text-dark-red/75">
			a <a href="hackclub.com" target="blank" class="text-red/75 underline hover:decoration-wavy"
				>hack club</a
			>
			project •
			<a
				href="slack.hackclub.com"
				target="_blank"
				class="text-red/75 underline hover:decoration-wavy">join the slack</a
			>
			•
			<a
				href="https://hackclub.enterprise.slack.com/team/U08H34LLQQJ"
				target="_blank"
				class="text-red/75 underline hover:decoration-wavy"
			>
				dm me!
			</a>
		</h2>
	</div>
</main>
