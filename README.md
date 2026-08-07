# hack club photobooth!!

<img src="https://i.postimg.cc/qqHfVtfC/image.png" alt="" width="400">

## table of contents
---
| # | content |
| --- | ---|
|1. | [yap](#hack-club-photobooth) |
| 2. |[how to run this project](#how-to-run-this-project) |
|3. |[ how to add your own photo frames](#how-to-add-your-own-photo-frames) |
|4. |[ ai disclosure](#ai-disclosure) |
|5.  |[screenshots](#screenshots) | 

ever since the hack club internship, i've been reflecting on how hack club has changed my life and brought me so many wonderful memories. what better way to treasure said memories than taking pictures?

so, i made this photobooth with hack-club themed photo strips for hackclubbers to use at events, meetups, or just at home. maybe to put on an instagram story or to send to friends. lots of possibilities :p

even if you're not a hack clubber, try the photobooth out just for fun! maybe i'll add non-hackclub related photo strips if enough people care.

> note: some adblockers & browsers make the video preview/photo-taking just... not work. i don't know why.
>
> it works on all my devices - if you run into a problem, first try in incognito mode (to prevent adblockers) & **if problems persist** please make a [github issue](https://github.com/ikealoverkat/hc-photobooth/issues)!!! i'll do my best to fix them TT 

the ideas for the photo frames come from different memories hack clubbers shared in [this thread](https://hackclub.slack.com/archives/C0A95RBCDL0/p1785874841564319) on the hack club slack. special thanks to swarit, peter, owais, stelle, kc, jenin, azzy, dwait, safia, shreya, lilia, isa, erin, and everyone else who shared their experiences! it's so fun reading them.

if you have a favourite hack club memory, pls tell me and i will maybe draw a frame inspired by it! :0

if you want to draw a frame, please do!!!!!!!!!!! [instructions + **template** here](#how-to-add-your-own-photo-frames)

### made with
- [svelte 5](https://svelte.dev/)
- icons from [lucide](https://lucide.dev/)
- deployed with [vercel](https://vercel.com)

### cool things in this project (well. i think they're cool)
- photobooth (no way really?)
- printing animation when you get the photo strip (go go go take photos to see it)
- gallery where you can save photos you've taken (stored in localStorage) and export them (download or copy paste)!

### check it out here! [photobooth.kat.wang](https://photobooth.kat.wang)

---

## how to run this project

1. clone the source code
``` sh
git clone https://github.com/ikealoverkat/hc-photobooth.git
```

2. install project dependencies
``` sh
npm install
```

3. start the dev server
```sh
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

to build the site: 
```sh
npm run build
```


## how to add your own photo frames
draw/edit this template:

![template](https://i.postimg.cc/d0PzpkGx/image.png)

*download link: https://i.postimg.cc/d0PzpkGx/image.png*

the dimensions are **450px x 1500px**.

**you can either:**
- fork the repo & make a pull request for your changes 
- [send me](https://kat.wang) your frame with a name and description, and i'll add it :) *(may take a while)*

### instructions for making a pull request
1. add the image file to `/static/`
2. in `+page.svelte`, add your frame to this array of objects:
``` ts
let frames: Frame[] = $state([
		{ src: '/frame_hc.png', name: 'hack club themed frame!', selected: false },
		{ src: '/frame_hctg.png', name: 'hack club the frame', selected: false },
		{ src: '/frame_intern.png', name: 'summer internship frame!', selected: false },
		{ src: '/frame_ovg.png', name: 'overglade frame. senator im singaporean', selected: false },
		{ src: '/frame_ysws.png', name: 'ysws frame! dont we all love free stuff?', selected: false },
        // like this. make sure selected is false!
        {src: '/your_frame.png', name: 'your frame name', selected: false}
	]);
```
3. in `/frames/+page.svelte`, add it to this array of objects. write a longer description for your frame & the story behind it ^_^
``` ts

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
		// etc...
        {
            src: '/your_frame.png',
            name: 'your frame name',
            description: 'your frame description'
        }
]
```
4. make your pull request! i'll try to get to it ASAP. :)


## AI DISCLOSURE
no ai was used for this project, except for the vscode copilot autocomplete 

## screenshots

ooooooooh project

<div style="display: flex; gap: 2rem; flex-wrap: wrap; margin: 2rem;">
    <img src="https://i.postimg.cc/qqHfVtfC/image.png" alt="landing page" style="width: 200px">
    <img src="https://i.postimg.cc/02HfbTjx/image.png" alt="printed photostrip" style="width: 200px">
    <img src="https://i.postimg.cc/C1YC3YPj/image.png" alt="gallery page" style="width: 200px">
    <img src="https://i.postimg.cc/90Qdfbjz/image.png" alt="frames page" style="width: 200px">
    <img src="https://i.postimg.cc/CKpb8SVj/image.png" alt="frames page (hover)" style="width: 200px">                
</div>

---

made w/ <3 and monster energy by [kat wang](https://kat.wang)

(again) check out the project at [photobooth.kat.wang](https://photobooth.kat.wang). contact me if u have ideas for any features or if there are any problems!

and if you aren't in hack club, [check it out](https://hackclub.com) :) it's wondeerful!!!
