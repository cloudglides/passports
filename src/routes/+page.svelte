<script lang="ts">
	import dataPage from '#lib/assets/data-page.png';
	import flag from '#lib/assets/hack-club-flag.png';
	import mascot from '#lib/assets/embassy-mascot.png';
	import ScrambleText from '#lib/ScrambleText.svelte';

	// Enough cards to run corner to corner on wide screens.
	const strip = Array.from({ length: 10 });

	let scramble = $state(0);
	const replay = () => scramble++;
</script>

<!--
	Laid out on the 524 × 344 reference frame: --u is 1/524 of the viewport width.
-->
<main class="stage">
	<div class="strip" aria-hidden="true">
		<div class="track">
			{#each strip as _, i (i)}
				<img class="card" src={dataPage} alt="" />
			{/each}
		</div>
	</div>

	<h1 class="title">
		Passport<br /><span class="office"
			>Office<img class="mascot" src={mascot} alt="" /></span
		>
	</h1>

	<a class="login" href="/login" onpointerenter={replay} onfocus={replay}>
		<ScrambleText text="passport>>>login>>>>>>43" trigger={scramble} />
	</a>

	<footer class="foot">
		<div class="brand">
			<img src={flag} alt="" />
			<span>Hack Club</span>
		</div>
		<p>
			The Hack Foundation is a 501(c)(3) public <strong>charity</strong> with the EIN <span class="nowrap">81-2908499</span>
		</p>
	</footer>
</main>

<style>
	.stage {
		--u: min(100vw / 524, 100dvh / 344);
		position: relative;
		min-height: 100vh;
		min-height: 100dvh;
		overflow: hidden;
		background: #d9d9d9;
		color: #000;
	}

	.title {
		position: absolute;
		z-index: 1;
		margin: 0;
		left: calc(7 * var(--u));
		top: calc(30 * var(--u));
		font-family: 'PixelHackers', monospace;
		font-weight: normal;
		font-size: calc(48 * var(--u));
		line-height: 0.98;
		text-transform: uppercase;
		letter-spacing: 0.01em;
	}

	/* Sits in the gap after OFFICE, under the end of PASSPORT, without affecting line layout. */
	.office {
		position: relative;
	}

	.mascot {
		position: absolute;
		/* ~1.35× the cap height, centred on the caps and in the gap before PASSPORT's T. */
		left: calc(100% + 0.3em);
		bottom: 0.16em;
		height: 0.89em;
		width: auto;
		transform: rotate(-4deg);
		pointer-events: none;
	}

	.login {
		position: absolute;
		z-index: 1;
		left: calc(7 * var(--u));
		top: calc(159 * var(--u));
		width: calc(166 * var(--u));
		height: calc(24 * var(--u));
		display: flex;
		align-items: center;
		padding-left: calc(6 * var(--u));
		border-radius: calc(2.5 * var(--u));
		/* Colours sampled from the wave band at the bottom of the data page. */
		background:
			url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='1.1' numOctaves='2' stitchTiles='stitch'/%3E%3CfeColorMatrix values='0 0 0 0 1 0 0 0 0 1 0 0 0 0 1 0 0 0 -1.6 1.1'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.35'/%3E%3C/svg%3E"),
			/* Guilloche: interlocking sine waves, the security print on passport pages. */
			url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='48' height='10'%3E%3Cg fill='none' stroke='white' stroke-width='0.7' opacity='0.5'%3E%3Cpath d='M0 5C6 0 18 0 24 5S42 10 48 5'/%3E%3Cpath d='M0 5C6 10 18 10 24 5S42 0 48 5'/%3E%3C/g%3E%3C/svg%3E"),
			/* Diamond lattice, as on the data page's wave band. */
			repeating-linear-gradient(60deg, transparent 0 9px, rgb(255 255 255 / 0.14) 9px 10px),
			repeating-linear-gradient(-60deg, transparent 0 9px, rgb(255 255 255 / 0.14) 9px 10px),
			linear-gradient(90deg, #b47fdd 0%, #b17ada 30%, #a375d8 55%, #8d73d9 80%, #7576d8 100%);
		color: #000;
		font-family: 'PolySans', sans-serif;
		font-size: calc(13 * var(--u));
		text-decoration: none;
		white-space: nowrap;
	}

	.login:focus-visible {
		outline: 2px solid #000;
		outline-offset: 2px;
	}

	.strip {
		position: absolute;
		/* centre sits 201 × 108 frame units in from the bottom-right corner */
		left: calc(100% - 201 * var(--u));
		top: calc(100% - 108 * var(--u));
		transform: translate(-50%, -50%) rotate(-41deg);
		pointer-events: none;
	}

	/*
		Lighting is fixed to .strip, not the moving .track, so the cards slide
		under a stationary light like paper under a lamp.
	*/
	.strip::before,
	.strip::after {
		content: '';
		position: absolute;
		inset: 0;
		z-index: 1;
		pointer-events: none;
	}

	/* Highlights: lit top edge, a soft specular hotspot, and a faint glint band. */
	.strip::before {
		mix-blend-mode: screen;
		background:
			linear-gradient(180deg, rgb(255 255 255 / 0.35), transparent 14%),
			radial-gradient(18% 120% at 58% 35%, rgb(255 255 255 / 0.3), transparent 70%),
			linear-gradient(
				100deg,
				transparent 40%,
				rgb(255 240 255 / 0.18) 46%,
				rgb(255 255 255 / 0.32) 48%,
				rgb(220 235 255 / 0.18) 50%,
				transparent 56%
			);
	}

	/* Shading: light falls off toward the ends and the lower edge. */
	.strip::after {
		mix-blend-mode: multiply;
		background:
			linear-gradient(180deg, transparent 60%, rgb(60 30 120 / 0.22)),
			linear-gradient(
				90deg,
				rgb(70 50 130 / 0.3),
				transparent 30%,
				transparent 75%,
				rgb(70 50 130 / 0.25)
			);
	}

	/* One card-width of travel, looped: identical cards make the seam invisible. */
	.track {
		display: flex;
		box-shadow:
			0 calc(2 * var(--u)) calc(3 * var(--u)) rgb(40 20 90 / 0.18),
			0 calc(8 * var(--u)) calc(18 * var(--u)) rgb(40 20 90 / 0.22);
		animation: feed 9s linear infinite;
		will-change: transform;
	}

	@keyframes feed {
		from {
			transform: translateX(calc(-163 * var(--u)));
		}
		to {
			transform: translateX(0);
		}
	}

	@media (prefers-reduced-motion: reduce) {
		.track {
			animation: none;
		}
	}

	.card {
		flex: none;
		display: block;
		height: auto;
		width: calc(163 * var(--u));
	}

	.foot {
		position: absolute;
		z-index: 1;
		right: 0;
		bottom: calc(5 * var(--u));
		width: calc(144 * var(--u));
		font-family: 'PolySans', sans-serif;
	}

	.brand {
		display: flex;
		align-items: center;
		gap: calc(6 * var(--u));
		font-size: calc(14.5 * var(--u));
		line-height: 1;
	}

	.brand img {
		width: calc(56 * var(--u));
		transform: rotate(-2deg);
	}

	.foot p {
		margin: calc(3 * var(--u)) 0 0;
		font-size: calc(9.4 * var(--u));
		line-height: 1.12;
	}

	.nowrap {
		white-space: nowrap;
	}

	.foot strong {
		font-weight: bold;
	}
	/* Portrait / phones: size everything off the width so text stays legible. */
	@media (max-aspect-ratio: 1/1) {
		.stage {
			--u: min((100vw - 32px) / 250, 100dvh / 520);
		}

		.title,
		.login {
			left: 16px;
		}

		.title {
			top: calc(24 * var(--u));
		}

		.login {
			top: calc(140 * var(--u));
		}

		.strip {
			left: 50%;
			top: 60%;
		}

		.foot {
			right: 16px;
			bottom: 16px;
		}
	}
</style>
