<script lang="ts">
    import { browser } from '$app/environment';
    import { goto } from '$app/navigation';
    import PushButton from '$lib/PushButton.svelte';
    let theme = $state(browser ? localStorage.getItem('theme') ?? 'light' : 'light');
    $effect(() => {
        document.documentElement.setAttribute('data-theme', theme);
        localStorage.setItem('theme', theme);
        document.body.classList.add('landing');
        return () => document.body.classList.remove('landing');
    })
    function toggle() {
        theme = theme === 'light' ? 'dark' : 'light';
    }
    let glow:HTMLElement;
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
        function onMove(e:MouseEvent) {
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
<div class="landing">
    <h1>hi, i'm hypigel</h1>
    <PushButton onclick={() => goto('/about')}>about me</PushButton>
    <PushButton onclick={toggle}>{theme === 'light' ? 'dark' : 'light'} mode</PushButton>
</div>
<style>
    .glow {
        position: fixed;
        inset: -50%;
        pointer-events: none;
        z-index: -1;
        background: linear-gradient(135deg,transparent 42%,var(--glow) 50%,transparent 58%);
    }
    .landing {
        height:100dvh;
        display:flex;
        flex-direction:column;
        justify-content:center;
        align-items:center;
        gap: 1rem;
    }
    h1 {
        font-size: 4rem;
    }
    :global(body.landing) {
        margin: 0;
        overflow: hidden;
    }
</style>