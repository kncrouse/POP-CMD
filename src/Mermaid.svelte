<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js">
    import mermaid from 'mermaid';
    import {onMount} from 'svelte';

    export let theme = 'neutral'

    onMount(() => {
        mermaid.initialize({
            startOnLoad:true,
            theme
        });
    })
</script>

<div class="mermaid">
    <slot></slot>
</div>