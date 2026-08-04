<script lang="ts">
	import { onMount } from 'svelte';

	let video: HTMLVideoElement;
	let canvas: HTMLCanvasElement;

	let stream: MediaStream;
    let photos: string[] = $state([]);
    
    let countdown: number = $state(0);

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

        photos = [...photos, canvas.toDataURL('image/png')];
    }

    async function photobooth() {
        photos = [];

        for (let i = 0; i < 4; i++) {
            countdown = 3;
            
            while (countdown > 0) {
                await new Promise(r => setTimeout(r, 1000));
                countdown--;
            }

            takePhoto();
            if (i !== 3)
                await new Promise(r => setTimeout(r, 500));            
        }

    }

</script>

<h1>i said hey whats up hello</h1>

<div class="flex flex-col items-center justify-center gap-2">
    {#if countdown !== 0}
        <div class="text-4xl">{countdown}</div>
    {/if}
    <video bind:this={video} class="scale-x-[-1]"></video>
</div>

<button onclick={photobooth}>take photo</button>

<canvas bind:this={canvas} class="hidden">
</canvas>


<div class="scale-x-[-1] flex flex-row gap-2 self-end">
    {#each photos as photo}
        <img src={photo} alt="" width="150" />
    {/each}
</div>
