<script>
    export let questionText;
    import { createEventDispatcher } from 'svelte';
    export let items = [];
    let selected = false

    const handleAdd = () => {
        items = [heterid.value, ...items]
        heterid.value = ''
    }
</script>


<form style="align-content:flex-start;">
    <input type="checkbox" bind:checked={selected} on:click|stopPropagation>  {questionText}<br>

    <!-- svelte-ignore empty-block -->
    {#if selected }
        {#each items as item}
            <p>{item}</p>
        {/each}

        <div style="display:flex; height:100%;">
            <input id="heterid" type="text" placeholder="Add driver here"><br>
            <button on:click={handleAdd} >&plus;</button>
        </div>     
    {/if}


    
</form>




<style>

</style>