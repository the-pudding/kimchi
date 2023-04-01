<script>
	import * as Tone from 'tone'
	import { onMount } from 'svelte';
	export let chapter;
	export let chapterTracker = Number(chapter);
	let synth;
	let synth2;
	let now;
	let mounted = false;
	let soundon = false;
	let measure = 1600;
	let scales = ["A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab"];
	let baseKey = 8;
	let key = baseKey;
	let scaleObj = {
		"major": [0,2,4,5,7,9,11],
		"minor": [0,2,3,5,7,8,10],
		"dim": [0,1,3,4,5,7,9,10]
	}
	let currentScale = "major";
	let progressionStep = 0;
	// let chordProgression = [
	// 	[1,"minor"]
	// ]
	let chordProgression = [
		[0,"major"],
		[9,"minor"],
		[5,"major"],
		[7,"major"]
	]
	// let chordProgression = [
	// 	[0,"major"],
	// 	[5,"major"],
	// 	[0,"major"],
	// 	[0,"major"],
	// 	[7,"major"],
	// 	[0,"major"],
	// 	[0,"major"],
	// 	[5,"major"],
	// 	[0,"major"],
	// 	[5,"major"],
	// 	[4,"minor"],
	// 	[-1,"minor"],
	// 	[4,"minor"],
	// 	[9,"minor"],
	// 	[4,"minor"],
	// 	[3,"minor"],
	// 	[9,"minor"],
	// 	[0,"major"],
	// 	[7,"major"],
	// 	[5,"major"],
	// 	[7,"major"]
	// 	[0,"major"]
	// ]
	onMount(async () => {
		const vol = new Tone.Volume(-12).toDestination();
		synth2 = new Tone.Oscillator().connect(vol);
		synth = new Tone.PolySynth(Tone.Synth).toDestination();
		now = Tone.now();
		mounted = true;
	});
	function soundToggle() {
		soundon = !soundon;
	}
	
	function play(n, length, instrument) {
		if (mounted) {
			if (soundon) {
				now = Tone.now();
				if (instrument == 4) {
					synth2.triggerAttack(n, now);
				} else {
					synth.triggerAttack(n, now);
				}
			}
			try {
				synth.triggerRelease([n], now + length);
				synth2.triggerRelease([n], now + length);
			} catch {
			
			}
		}
	}

	let counter = 1;
	setInterval(function() {		
		if (counter % 64 == 0) {
			progressionStep +=1;
			if (progressionStep >= chordProgression.length - 1) {
				progressionStep = 0;
			} 
			
			key = baseKey + chordProgression[progressionStep][0];
			currentScale = chordProgression[progressionStep][1];
			counter = 0;
		}
		counter++;
		
		playNote("1",measure/32/1000,8,false, true);
		playNote("2",measure/4/1000,16,true, true);
		playNote("3",measure/4/1000,8, true, true);
		//playNote("4",measure/2/1000,16, true, true);
		
		//playChord("3",measure/2/1000,8, true);
	}, measure/32);
	
	function playNote(pitch,len,interval,rand,chordKey) {
		if (counter % interval == 0) {
			let notes = scaleObj[currentScale];
			let keyShift = notes[Math.floor(notes.length*Math.random())] + key;
			if (chordKey) {
				let chordShift = [0,2,4].randFromArray();
				keyShift = notes[chordShift] + key;
			}
			let m = scales[keyShift];
			if (!rand || (rand && Math.random() < 0.4) ) {
				play(m + pitch, len);
			}
		}
	}
	
	function playChord(pitch,len, interval, rand) {
		if (counter % interval == 0) {
			let notes = scaleObj[currentScale];
			if (!rand || (rand && Math.random() < 0.2) ) {
				play(scales[key] + pitch, len, pitch);
				play(scales[key + notes[2] + 12] + pitch, len, pitch);
				//play(scales[key + notes[4]] + pitch, len) + 1;
			}
		}
	}

	Array.prototype.randFromArray = function(){
	  return this[Math.floor(Math.random()*this.length)];
	}

</script>	
<div class="soundbar">
	{#if soundon}
		<div class="soundbutton" on:keydown={soundToggle} on:click={soundToggle}>Sound off</div>
	{:else}
		<div class="soundbutton" on:keydown={soundToggle} on:click={soundToggle}>Sound on</div>
	{/if}
</div>
<style>
	.soundbar { text-align: right; }
	.soundbutton {
		cursor: pointer;
		color: #333;
		display: inline-block;
	}
	.soundbutton:hover {
		color: black;
	}
</style>