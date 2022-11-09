<script>
    export let questionText;
    import { createEventDispatcher } from 'svelte';
    export let items = [];

    $: console.log(items)

    const handleAdd = () => {
        items = [addInput.value, ...items]
        addInput.value = ''
    }

    const handleDelete = (id) => {
        items = items.filter((item) => item != id)
    }
</script>


<form style="align-items:stretch">
    <p>{questionText}</p>

    {#each items as item}
        <div style="display:flex; height:100%;">
            <input type="checkbox" on:click|stopPropagation>  {item}<br>
        </div>
                
    {/each}   

</form>


<style>

</style>