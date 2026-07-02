<script lang="ts">
	import { browser } from '$app/environment';
	import { goto } from '$app/navigation';
	import PushButton from '$lib/PushButton.svelte';
	import { supabase } from '$lib/supabase';

	let theme = $state(browser ? localStorage.getItem('theme') ?? 'light' : 'light');
	$effect(() => {
		document.documentElement.setAttribute('data-theme', theme);
		localStorage.setItem('theme', theme);
	});
	function toggle() {
		theme = theme === 'light' ? 'dark' : 'light';
	}

	const FEEDBACK_LIMIT = 2;
	let word = $state('');
	let words = $state<string[]>([]);
	let submitting = $state(false);
	let used = $state(browser ? Number(localStorage.getItem('feedbackCount') ?? 0) : 0);
	let atLimit = $derived(used >= FEEDBACK_LIMIT);

	async function loadWords() {
		const { data } = await supabase
			.from('feedback')
			.select('word')
			.order('created_at', { ascending: false })
			.limit(12);
		if (data) words = data.map((r) => r.word);
	}

	async function submitWord() {
		const clean = word.trim().split(/\s+/)[0];
		if (!clean || submitting || atLimit) return;
		submitting = true;
		const { error } = await supabase.from('feedback').insert({ word: clean });
		submitting = false;
		if (!error) {
			word = '';
			used += 1;
			if (browser) localStorage.setItem('feedbackCount', String(used));
			await loadWords();
		}
	}

	$effect(() => {
		loadWords();
	});

	let count = $state(browser ? Number(localStorage.getItem('count') ?? 0) : 0);
	$effect(() => {
		localStorage.setItem('count', String(count));
	});

	let glow: HTMLElement;
	$effect(() => {
		let x = 0, y = 0, mx = 0, my = 0;
		let frame = 0;
		let running = false;
		function tick() {
			x += (mx - x) * 0.08;
			y += (my - y) * 0.08;
			glow.style.transform = `translate(${x}px, ${y}px)`;
			if (Math.abs(mx - x) > 0.1 || Math.abs(my - y) > 0.1) {
				frame = requestAnimationFrame(tick);
			} else {
				running = false;
			}
		}
		function onMove(e: MouseEvent) {
			mx = (e.clientX - window.innerWidth / 2) * 0.15;
			my = (e.clientY - window.innerHeight / 2) * 0.15;
			if (!running) { running = true; frame = requestAnimationFrame(tick); }
		}
		window.addEventListener('mousemove', onMove);
		return () => {
			window.removeEventListener('mousemove', onMove);
			cancelAnimationFrame(frame);
		};
	});
</script>

<div class="glow" bind:this={glow}></div>

<main class="page">
	<nav class="bar">
		<PushButton onclick={() => goto('/')}>home</PushButton>
		<PushButton onclick={toggle}>{theme === 'light' ? 'dark' : 'light'} mode</PushButton>
	</nav>

	<header class="intro">
		<span class="eyebrow">about</span>
		<h1>hi, i'm hypigel</h1>
		<p class="lead">
			i like building random stuff
		</p>
	</header>

	<section class="grid">
		<article class="card wide">
			<h2>what this is</h2>
			<p>
				a personal site i'm using to learn <a href="https://svelte.dev">svelte</a>.
				the gradient, the light that follows your cursor, the glass buttons, they all
				are little pieces i built along the way.
			</p>
		</article>

		<article class="card">
			<div class="shell">
				<div class="core">
					<span class="stat">{count}</span>
					<span class="stat-label">total clicks</span>
				</div>
			</div>
			<PushButton onclick={() => (count += 1)}>click me</PushButton>
		</article>

		<article class="card">
			<h2>make it yours</h2>
			<form class="field" onsubmit={(e) => { e.preventDefault(); submitWord(); }}>
				<span>one word for this site</span>
				<input bind:value={word} placeholder="cool" maxlength="40" disabled={atLimit} />
			</form>
			{#if atLimit}
				<p class="limit-note">that's your two - thanks for the feedback!</p>
			{/if}
			{#if words.length}
				<div class="words">
					<span class="words-label">others said</span>
					<p class="word-list">{words.join(' · ')}</p>
				</div>
			{/if}
		</article>
	</section>
</main>

<style>
	.glow {
		position: fixed;
		inset: -50%;
		pointer-events: none;
		z-index: -1;
		background: linear-gradient(135deg, transparent 42%, var(--glow) 50%, transparent 58%);
	}

	.page {
		max-width: 760px;
		margin: 0 auto;
		padding: 4rem 1.5rem 6rem;
		display: flex;
		flex-direction: column;
		gap: 3rem;
	}

	.bar {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.intro {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		animation: rise 800ms cubic-bezier(0.32, 0.72, 0, 1) both;
	}
	.eyebrow {
		align-self: flex-start;
		padding: 0.3rem 0.8rem;
		border-radius: 999px;
		border: 1px solid var(--btn-border);
		background: var(--btn-bg);
		font-size: 0.7rem;
		text-transform: uppercase;
		letter-spacing: 0.2em;
		opacity: 0.8;
	}
	h1 {
		font-size: clamp(2.5rem, 8vw, 4rem);
		line-height: 1.05;
		margin: 0;
	}
	.lead {
		font-size: 1.15rem;
		line-height: 1.6;
		max-width: 52ch;
		opacity: 0.85;
		margin: 0;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 1.25rem;
	}
	.card {
		display: flex;
		flex-direction: column;
		gap: 1.1rem;
		align-items: flex-start;
		padding: 1.75rem;
		border-radius: 22px;
		border: 1px solid var(--btn-border);
		background: var(--btn-bg);
		box-shadow:
			inset 0 1px 0 var(--btn-highlight),
			0 24px 50px -30px var(--btn-shadow);
		animation: rise 800ms cubic-bezier(0.32, 0.72, 0, 1) both;
	}
	.card.wide {
		grid-column: 1 / -1;
	}
	.card:not(.wide) {
		align-items: center;
		text-align: center;
	}
	.card:nth-child(1) { animation-delay: 80ms; }
	.card:nth-child(2) { animation-delay: 160ms; }
	.card:nth-child(3) { animation-delay: 240ms; }

	.card h2 {
		margin: 0;
		font-size: 1.15rem;
		font-weight: 600;
	}
	.card p {
		margin: 0;
		line-height: 1.6;
		opacity: 0.85;
	}

	.shell {
		width: 100%;
		padding: 6px;
		border-radius: 18px;
		border: 1px solid var(--btn-border);
		background: var(--btn-bg);
	}
	.core {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.35rem;
		padding: 1.75rem 1.5rem;
		border-radius: 14px;
		background: var(--well-bg);
		box-shadow: inset 0 2px 5px var(--well-shadow);
	}
	.stat {
		font-size: 2.75rem;
		font-weight: 700;
		font-variant-numeric: tabular-nums;
		line-height: 1;
	}
	.stat-label {
		font-size: 0.75rem;
		text-transform: uppercase;
		letter-spacing: 0.15em;
		text-indent: 0.15em;
		opacity: 0.6;
	}

	.field {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		width: 100%;
		font-size: 0.85rem;
		opacity: 0.85;
	}
	.field input {
		width: 100%;
		padding: 0.7rem 0.95rem;
		border-radius: 12px;
		border: 1px solid var(--btn-border);
		background: var(--well-bg);
		box-shadow: inset 0 2px 5px var(--well-shadow);
		color: var(--text);
		font-size: 1rem;
		text-align: center;
		outline: none;
		transition: border-color 250ms ease, box-shadow 250ms ease;
	}
	.field input:focus-visible {
		border-color: var(--btn-ring);
		box-shadow:
			inset 0 2px 5px var(--well-shadow),
			0 0 0 3px var(--btn-ring);
	}
	.field input:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}
	.limit-note {
		margin: 0;
		font-size: 0.85rem;
		opacity: 0.7;
	}
	.words {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.4rem;
		width: 100%;
	}
	.words-label {
		font-size: 0.7rem;
		text-transform: uppercase;
		letter-spacing: 0.15em;
		text-indent: 0.15em;
		opacity: 0.6;
	}
	.word-list {
		margin: 0;
		line-height: 1.7;
		opacity: 0.85;
		word-break: break-word;
	}

	a {
		color: inherit;
		text-underline-offset: 3px;
		text-decoration-color: var(--btn-ring);
	}

	@keyframes rise {
		from { opacity: 0; transform: translateY(24px); filter: blur(6px); }
		to   { opacity: 1; transform: translateY(0);    filter: blur(0); }
	}

	@media (max-width: 640px) {
		.grid { grid-template-columns: 1fr; }
	}
</style>
