<script lang="ts">
	import { onMount } from 'svelte';

	let video: HTMLVideoElement;
	let canvas: HTMLCanvasElement;

	let stream: MediaStream;

	onMount(async () => {
		stream = await navigator.mediaDevices.getUserMedia({ video: true });
		video.srcObject = stream;
        await video.play();
	});

    function takePhoto() {
        const context = canvas.getContext('2d');

        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;

        context?.drawImage(video, 0, 0, video.videoWidth, video.videoHeight);

        const image = canvas.toDataURL('image/png');

        console.log(image);
    }
</script>

<h1>i said hey whats up hello</h1>

<video bind:this={video} class="scale-x-[-1]"></video>

<button onclick={takePhoto}>take photo</button>

<canvas bind:this={canvas} class="scale-x-[-1]"></canvas>
