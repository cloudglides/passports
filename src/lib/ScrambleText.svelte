<script lang="ts">
	import { onMount } from 'svelte';

	// Only letters and digits scramble; symbols like > stay put.
	const LETTERS = 'abcdefghijklmnopqrstuvwxyz';
	const DIGITS = '0123456789';

	let {
		text,
		trigger = 0,
		duration = 1600,
		interval = 70
	}: { text: string; trigger?: number; duration?: number; interval?: number } = $props();

	// Scrambled glyph per slot; null once a slot has resolved to the real character.
	let noise = $state<(string | null)[]>([]);
	let frame = 0;

	const pick = (set: string) => set[Math.floor(Math.random() * set.length)];

	const scrambles = (ch: string) => /[a-z0-9]/i.test(ch);
	const rand = (ch: string) => pick(/\d/.test(ch) ? DIGITS : LETTERS);

	function run() {
		if (matchMedia('(prefers-reduced-motion: reduce)').matches) return;
		cancelAnimationFrame(frame);
		const start = performance.now();
		let last = -Infinity;
		const tick = (now: number) => {
			const progress = Math.min((now - start) / duration, 1);
			// Swap glyphs every `interval` ms rather than every frame so it reads, not strobes.
			if (now - last >= interval || progress === 1) {
				last = now;
				const revealed = Math.floor(progress * text.length);
				noise = [...text].map((ch, i) => (i < revealed || !scrambles(ch) ? null : rand(ch)));
			}
			if (progress < 1) frame = requestAnimationFrame(tick);
		};
		frame = requestAnimationFrame(tick);
	}

	$effect(() => {
		if (trigger) run();
	});

	onMount(() => {
		run();
		return () => cancelAnimationFrame(frame);
	});
</script>

<!-- Each slot keeps the real character's width, so the scramble never reflows. -->
<span class="text" aria-hidden="true"
	>{#each text as ch, i}<span class="slot"
			><span class:hidden={noise[i]}>{ch}</span>{#if noise[i]}<span class="noise">{noise[i]}</span
				>{/if}</span
		>{/each}</span
><span class="sr">{text}</span>

<style>
	.slot {
		position: relative;
	}

	.hidden {
		visibility: hidden;
	}

	.noise {
		position: absolute;
		left: 50%;
		top: 0;
		transform: translateX(-50%);
	}

	.sr {
		position: absolute;
		width: 1px;
		height: 1px;
		overflow: hidden;
		clip-path: inset(50%);
		white-space: nowrap;
	}
</style>
