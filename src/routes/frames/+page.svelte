<script lang="ts">
	import Footer from '$lib/Footer.svelte';
    
    type Frame = {
        src: String,
        name: String,
        
    }

</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	<div
		class="m-8 flex min-h-screen w-10/16 flex-col items-center gap-4 rounded-2xl bg-white/75 p-16 outline-2 outline-dark-red/15"
	>
		<h1 class="text-4xl font-semibold text-red">frames frames frames</h1>
		<!-- yap -->
		<div>
			<p class="text-center text-xl text-dark-red">
				in my <a
					href="https://hackclub.slack.com/archives/C0A95RBCDL0/p1785874841564319"
					class="text-red underline hover:decoration-wavy">personal channel,</a
				>
				i asked hack clubbers what their favourite memory from hack club was, and drew these frames based
				on their stories. you can read about those memories here!
			</p>
			<hr class="opacity-0 m-2">
            <p class="text-center text-xl text-dark-red">
				i'll always want to add more frames! send me a dm with your favourite hack club memory, or
				draw your own frame & make a
				<a
					href="https://github.com/ikealoverkat/hc-photobooth"
					class="text-red underline hover:decoration-wavy">pull request.</a
				> ^_^
			</p>
		</div>
        <!-- frames -->
         <div>
            
         </div>
	</div>
	<Footer />
</main>
