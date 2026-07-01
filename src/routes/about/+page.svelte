<script lang="ts">
    import { browser } from '$app/environment';
    let txt = $state('cool!');
    let count = $state(browser ? Number(localStorage.getItem('count') ?? 0) : 0);
    $effect(() => {
        localStorage.setItem('count', String(count));
    })
    function inc() {
        count += 1;
    }
</script>

<h1>hi, i'm hypigel</h1>
<p>this page was built with <a href="https://svelte.dev">svelte</a></p>
<label>
    customize the site:
    <input bind:value={txt}/>
</label>
<!-- svelte-ignore event_directive_deprecated -->
<button on:click={inc}>click me</button>
<p>you clicked {count} times</p>
<p>this site is so {txt}</p>
