<script lang="ts">
	let { children, onclick, arrow = false } = $props();
</script>

<button class="btn" {onclick}>
	<span class="label">{@render children()}</span>
	{#if arrow}
		<span class="icon" aria-hidden="true">
			<svg viewBox="0 0 16 16" fill="none" stroke="currentColor"
				stroke-width="1.25" stroke-linecap="round" stroke-linejoin="round">
				<path d="M4.5 11.5L11.5 4.5" />
				<path d="M6 4.5h5.5V10" />
			</svg>
		</span>
	{/if}
</button>

<style>
	.btn {
		position: relative;
		display: inline-flex;
		align-items: center;
		gap: 0.55rem;
		padding: 0.7rem 0.8rem 0.7rem 1.4rem;
		border: 1px solid var(--btn-border);
		border-radius: 999px;
		font-size: 1rem;
		font-weight: 500;
		letter-spacing: 0.01em;
		color: var(--text);
		background: var(--btn-bg);
		backdrop-filter: blur(12px) saturate(140%);
		-webkit-backdrop-filter: blur(12px) saturate(140%);
		box-shadow:
			inset 0 1px 0 var(--btn-highlight),
			0 10px 30px -14px var(--btn-shadow);
		cursor: pointer;
		will-change: transform;
		transition:
			transform 500ms cubic-bezier(0.32, 0.72, 0, 1),
			box-shadow 500ms cubic-bezier(0.32, 0.72, 0, 1),
			background 300ms ease;
	}

	.btn:not(:has(.icon)) {
		padding: 0.7rem 1.4rem;
	}

	.btn:hover {
		transform: translateY(-2px);
		background: var(--btn-bg-hover);
		box-shadow:
			inset 0 1px 0 var(--btn-highlight),
			0 18px 40px -16px var(--btn-shadow);
	}

	.btn:active {
		transform: translateY(0) scale(0.98);
		transition-duration: 120ms;
	}

	.btn:focus-visible {
		outline: 2px solid var(--btn-ring);
		outline-offset: 3px;
	}

	.icon {
		display: grid;
		place-items: center;
		width: 1.7rem;
		height: 1.7rem;
		border-radius: 999px;
		background: var(--btn-icon-bg);
		transition: transform 450ms cubic-bezier(0.32, 0.72, 0, 1);
	}
	.icon svg {
		width: 0.85rem;
		height: 0.85rem;
	}
	.btn:hover .icon {
		transform: translate(2px, -2px);
	}
</style>
