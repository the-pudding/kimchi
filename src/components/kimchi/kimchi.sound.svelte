<script>
	import * as Tone from 'tone'
	import { onMount } from 'svelte';
	export let chapter;
	export let mode;
	let synth;
	let now;
	let mounted = false;
	let soundon = false;
	let measure = 4000;
	let scales = ["A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab"];
	let baseKey = 8;
	let key = baseKey;
	let progressionStep = -1;
	let sceneDarkness = {
		"0": "dark",
		"1": "dark",
		"2": "light",
		"3": "dark",
		"4": "light",
		"5": "dark",
		"6": "light",
		"7": "dark",
		"8": "light"
	}
	
	// each has 16
	let chordProgression = {
		"0": [
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[7, "7b9"],
			[9, "M"],
			[5, "M"],
			[7, "7#9"],
			[2, "m7b5"],
			[5, "M13"],
			[4, "M11"],
			[0, "M"]
		],
		"1": [
		  [0,"M"],
		  [4,"M"],
		  [2,"m"],
		  [5,"M7"],
		  [0,"M"],
		  [4,"M"],
		  [2,"m"],
		  [5,"M7"],
		  [7,"7"],
		  [9,"M"],
		  [5,"M"],
		  [7,"7"],
		  [2,"m"],
		  [5,"M"],
		  [4,"M"],
		  [0,"M"],
		  [9,"M7"],
		  [2,"m"],
		  [7,"7#9"],
		  [0,"M"],
		  [4,"M"],
		  [9,"M7"],
		  [5,"M"],
		  [7,"7"],
		  [2,"m"]
		],
		"2": [
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[7, "7b9"],
			[9, "M"],
			[5, "M"],
			[7, "7#9"],
			[2, "m7b5"],
			[5, "M13"],
			[4, "M11"],
			[0, "M"]
		],
		"3": [
		  [0,"M"],
		  [4,"M"],
		  [2,"m"],
		  [5,"M7"],
		  [0,"M"],
		  [4,"M"],
		  [2,"m"],
		  [5,"M7"],
		  [7,"7"],
		  [9,"M"],
		  [5,"M"],
		  [7,"7"],
		  [2,"m"],
		  [5,"M"],
		  [4,"M"],
		  [0,"M"],
		  [9,"M7"],
		  [2,"m"],
		  [7,"7#9"],
		  [0,"M"],
		  [4,"M"],
		  [9,"M7"],
		  [5,"M"],
		  [7,"7"],
		  [2,"m"]
		],
		"4": [
		  [1, "m7"],
		  [5, "7"],
		  [6, "M7"],
		  [3, "m7"],
		  [1, "m7"],
		  [5, "7"],
		  [6, "M7"],
		  [3, "m7"],
		  [0, "M7"],
		  [5, "m7"],
		  [9, "m7"],
		  [3, "m7"],
		  [0, "M7"],
		  [5, "m7"],
		  [9, "m7"],
		  [3, "m7"],
		],
		"5": [
		  [0, "M"],
		  [5, "M7"],
		  [9, "M"],
		  [7, "m"],
		  [0, "M"],
		  [5, "M7"],
		  [9, "M"],
		  [7, "m"],
		  [2, "m"],
		  [5, "M"],
		  [4, "M"],
		  [0, "M"],
		  [2, "m"],
		  [5, "M"],
		  [7, "m"],
		  [0, "M"]
		],
		"6": [
		  [0, "M"],
		  [5, "M7"],
		  [9, "M"],
		  [7, "m"],
		  [0, "M"],
		  [5, "M7"],
		  [9, "M"],
		  [7, "m"],
		  [2, "m"],
		  [5, "M"],
		  [4, "M"],
		  [0, "M"],
		  [2, "m"],
		  [5, "M"],
		  [7, "m"],
		  [0, "M"]
		],
		"7": [
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[7, "7b9"],
			[9, "M"],
			[5, "M"],
			[7, "7#9"],
			[2, "m7b5"],
			[5, "M13"],
			[4, "M11"],
			[0, "M"]
		],
		"8": [
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M7"],
			[7, "7b9"],
			[9, "M"],
			[5, "M"],
			[7, "7#9"],
			[2, "m7b5"],
			[5, "M13"],
			[4, "M11"],
			[0, "M"]
		]
	};
	let chord = "M";
	let chords = {
	  "M": [0, 4, 7],
	  "m": [0, 3, 7],
	  "aug": [0, 4, 8],
	  "dim": [0, 3, 6],
	  "sus2": [0, 2, 7],
	  "sus4": [0, 5, 7],
	  "7": [0, 4, 7, 10],
	  "M7": [0, 4, 7, 11],
	  "m7": [0, 3, 7, 10],
	  "mM7": [0, 3, 7, 11],
	  "6": [0, 4, 7, 9],
	  "m6": [0, 3, 7, 9],
	  "9": [0, 2, 4, 7, 10],
	  "M9": [0, 2, 4, 7, 11],
	  "m9": [0, 2, 3, 7, 10],
	  "11": [0, 2, 4, 5, 7, 10],
	  "M11": [0, 2, 4, 5, 7, 11],
	  "m11": [0, 2, 3, 5, 7, 10],
	  "13": [0, 2, 4, 7, 9, 10],
	  "M13": [0, 2, 4, 7, 9, 11],
	  "m13": [0, 2, 3, 7, 9, 10],
	  "add9": [0, 2, 4, 7],
	  "6add9": [0, 2, 4, 7, 9],
	  "aug7": [0, 4, 8, 10],
	  "aug9": [0, 4, 8, 10, 2],
	  "dim7": [0, 3, 6, 9],
	  "m7b5": [0, 3, 6, 10],
	  "7b5": [0, 4, 6, 10],
	  "7#5": [0, 4, 8, 10],
	  "7b9": [0, 4, 7, 10, 1],
	  "7#9": [0, 4, 7, 10, 3]
	}
	
	onMount(async () => {
		synth = new Tone.PolySynth(Tone.Synth).toDestination();
		now = Tone.now();
		mounted = true;
	});
	function soundToggle() {
		soundon = !soundon;
	}
	
	function play(n, length) {
		if (mounted) {
			if (soundon) {
				now = Tone.now();
					synth.triggerAttack(n, now);
			}
			synth.triggerRelease([n], now + length);
		}
	}

	let counter = 0;
	let timer = setInterval(song, measure/24);
	
	function song() {
		if (mounted && soundon) {
			if (counter % 16 == 0 || counter == 0) {
				progressionStep +=1;
				if (progressionStep >= chordProgression[chapter].length - 1) {
					progressionStep = 0;
				}
				key = chordProgression[chapter][progressionStep][0];
				chord = chordProgression[chapter][progressionStep][1];
				console.log(scales[key], chord);
			}
			
			if ([0,1].includes(chapter)) {
				playNote("1",measure/64/1000,2,true, true);
				playNote("2",measure/64/1000,2,false, true);
				playNote("3",measure/16/1000,4, true, false);
				playNote("4",measure/32/1000,6, true, false);
			} else if ([2].includes(chapter)) {
				playNote("1",measure/64/1000,1,true, true);
				playNote("2",measure/32/1000,4,false, false);
				playNote("3",measure/32/1000,2, true, false);
				playNote("4",measure/64/1000,1, true, false);
			} else if ([3].includes(chapter)) {
				playNote("2",measure/8/1000,4,false, true);
				playNote("3",measure/32/1000,3, true, false);
			} else if ([4].includes(chapter)) {
				//playNote("1",measure/64/1000,4,false, true);
				//playNote("2",measure/4/1000,16,false, true);
				playNote("2",measure/3/1000,16,false, true);
				playNote("4",measure/8/1000,4,false, false);
			} else if ([5,6].includes(chapter)) {
				playNote("1",measure/16/1000,4,true, true);
				playNote("2",measure/4/1000,16,false, true);
				playNote("3",measure/8/1000,8, true, false);
			} else if ([7].includes(chapter)) {
				playNote("1",measure/32/1000,4,false, true);
				playNote("2",measure/16/1000,6,false, true);
				playNote("3",measure/8/1000,16, true, false);
			} else {
				playNote("1",measure/128/1000,2,false, true);
				playNote("2",measure/64/1000,3,false, true);
				playNote("3",measure/16/1000,8, true, false);
			}
		} else if (mounted) {
			Tone.Transport.pause();
		}
		counter++;
	}
	
	function playNote(pitch,len,interval,rand,chordKey) {
		if (counter % interval == 0 && 64-(counter%64) > len ) {
			let keyShift = key;
			if (!chordKey) {
				let keyRand = Math.round(Math.abs(Math.sin(counter)) * 2);
				if (keyRand > 2) {
					len = len * 0.3;
				}
				keyShift = key + chords[chord][keyRand];
			}
			let m = scales[keyShift];
			if (!rand || (rand && Math.random() < 0.5) ) {
				if (rand && pitch == "4") {
					if (counter > 2) {
						play(m + pitch, len);
					}
				} else {
					play(m + pitch, len);
				}
			}
		}
	}
	

	Array.prototype.randFromArray = function(){
	  return this[Math.floor(Math.random()*this.length)];
	}
	$: {
		chapter = chapter;
		soundon = soundon;
		mode = mode;
	}
</script>	
<div class="soundbar">
	{#if soundon}
		
			{#if mode}
			<button class="soundbutton light" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound on button" src="assets/kimchi/universal/sound-on-light.png"/>
				Sound on
			</button>
			{:else}
			<button class="soundbutton {sceneDarkness[chapter]}" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound on button" src="assets/kimchi/universal/sound-on-{sceneDarkness[chapter]}.png"/>
				Sound on
			</button>
			{/if}
		
	{:else}
		
			{#if mode}
			<button class="soundbutton light" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound off button" src="assets/kimchi/universal/sound-off-light.png"/>
				Sound off
			</button>
			{:else}
			<button class="soundbutton {sceneDarkness[chapter]}" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound off button" src="assets/kimchi/universal/sound-off-{sceneDarkness[chapter]}.png"/>
				Sound off
			</button>
			{/if}
		
	{/if}
</div>
<style>
	.soundbar { text-align: right; }
	.soundbutton {
		cursor: pointer;
		display: inline-block;
		position: absolute;
		right: 0px;
		top: 0px;
		/* background: rgba(0,0,0,0.1); */
		width: 115px;
		border-radius: 0%;
		color: black;
		text-align: center;
		z-index: 9999;
		user-select: none;
		background: none;
	}
	.soundbutton.light {
		color: white;
	}
	.soundbutton img {
		width: 20px;
		height: 20px;
		margin-right: 5px;
		float: left;
	}
	.soundbutton:hover {
		color: black;
	}
</style>