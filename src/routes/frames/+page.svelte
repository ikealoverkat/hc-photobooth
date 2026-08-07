<script lang="ts">
	import Footer from '$lib/Footer.svelte';
	import { onMount } from 'svelte';

	type Frame = {
		src: String;
		name: String;
		description: String;
	};

	let frames = [
		{
			src: '/frame_hc.png',
			name: 'hack club frame',
			description: "there's actually no story behind this, i just wanted to draw heidi and orpheus."
		},
		{
			src: '/frame_hctg.png',
			name: 'hack club the frame',
			description:
				'a frame based on HCTG - hack club the game. it was an event in NYC in april 2026, but not a hackathon. just an event inspired by jet lag: the game! lots of us went to central park at ~10pm the first night and did karaoke. it was lots of fun. we also went to heytea at an unreasonable hour. and had too many celsiuses. celcii??? (cc owais & shurui) green team won! (cc stelle & peter)'
		},
		{
			src: '/frame_intern.png',
			name: 'summer intern frame',
			description:
				'a frame commemorating the 2026 hack club summer internship. i was an intern, along with 24 others, and we did tons of stupid things including (but not limited to) staying up until 4am watching obsession and other movies, abusing calpico, my shrimp plushie, getting ice cream & going to the beach, etc... if youre considering doing the HC internship, DO IT!!! (shoutout jenin for the idea, shoutout stelle ivie max jenin shurui candy matthew safia dhyan n yanella for being awesome sauce)'
		},
		{
			src: '/frame_ovg.png',
			name: 'overglade frame',
			description:
				'overglade overglade overglade. a super awesome event in singapore lead by tongyu & her team! going to the beach & having bbq, playing volleyball, singing karaoke, overdosing on monster (they went 5x over the snack budget iirc), clay buying a $30 basketball, owais bringing the diet coke up and down the stairs so i could NEVER FIND ANY, and like everything else was super memorable & fun. (thx owais for the idea)'
		},
		{
			src: 'frame_ysws.png',
			name: 'you ship photos, we ship photo strip',
			description:
				'hack club events are awesome, but a memory a lot of hack clubbers have is recieving their first item in the mail from hack club - whether it be free stickers, a YSWS prize, merch, or something else - and going: holy shit, this is real!! (cc swarit & his SOM a1 mini) i personally recieved my ipad from hack club after coding 90h and now i use it to make illustrations... for hack club. what goes around comes around...?'
		}
	];

	let hoverItem: HTMLAudioElement;

	onMount(() => {
		hoverItem = new Audio('/sounds/hover.wav');
	});

	function playHoverItem() {
		if (!hoverItem) return;
		hoverItem.currentTime = 0;
		hoverItem.play();
	}
</script>

<main class="dots-bg flex flex-col items-center justify-center gap-4 p-4">
	<div
		class="m-8 flex w-10/16 flex-col items-center gap-4 rounded-2xl bg-white/75 p-16 outline-2 outline-dark-red/15"
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
			<hr class="m-2 opacity-0" />
			<p class="text-center text-xl text-dark-red">
				i always want to add more frames! send me a dm with your favourite hack club memory, or draw
				your own frame & make a
				<a
					href="https://github.com/ikealoverkat/hc-photobooth"
					class="text-red underline hover:decoration-wavy">pull request.</a
				> ^_^
			</p>
		</div>
		<!-- frames -->
		<h1 class="mt-4 text-2xl text-dark-red italic">hover on a frame to read more!</h1>
		<div class="mt-4 -mr-12 flex flex-row justify-center">
			{#each frames as frame, i}
				<!-- svelte-ignore a11y_click_events_have_key_events -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<div class="group">
					<div class={i === 0 ? 'group' : 'group -ml-12'}>
						<img
							src={frame.src}
							alt={frame.name}
							class="relative w-35 outline transition-all duration-500 hover:z-20 hover:mx-6 hover:scale-103"
							onmouseenter={playHoverItem}
						/>
						<h2
							class="font-italic m-4 max-w-40 justify-self-center text-center font-phantom text-xl text-wrap text-dark-red opacity-0 duration-500 group-hover:opacity-100"
						>
							{frame.name}
						</h2>
					</div>
					<div
						class="frame-popup-fadein absolute inset-35 z-50 mx-auto hidden max-h-fit max-w-1/2 flex-col gap-2 rounded-2xl bg-white/95 p-8 outline outline-dark-red/45 drop-shadow-md group-hover:flex"
					>
						<h1 class="text-3xl font-bold text-red">{frame.name}</h1>
						<p class="text-2xl text-dark-red">{@html frame.description}</p>
					</div>
				</div>
			{/each}
		</div>
	</div>
	<Footer />
</main>
