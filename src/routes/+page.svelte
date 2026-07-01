<script lang="ts">
    import { browser } from '$app/environment';
    import { goto } from '$app/navigation';
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
</script>

<div class="landing">
    <h1>hi, i'm hypigel</h1>
    <button onclick={() => goto('/about')}>about me</button>
    <button onclick={toggle}>
    {theme === 'light' ? 'dark' : 'light'} mode
    </button>
</div>
<style>
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