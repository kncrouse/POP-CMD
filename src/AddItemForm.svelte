<script>
    export let questionText;
    export let placeholderText;
    import { createEventDispatcher } from 'svelte';
    export let items = [];
    export let questionChecked = false
    let inputText;

    let dispatch = createEventDispatcher();

    const handleClick = () => {
        dispatch('handleClick')
    }

    const handleAdd = () => {
        console.log(inputText)
        const item = {
            name: inputText,
            id: Math.random()
        }
        inputText = ''
        dispatch('addItem', item)
    }

    const handleDelete = (id) => {
        dispatch('deleteItem', id)
    }
</script>


<form style="align-items:stretch; margin:5px">
    <input type="checkbox" bind:checked={questionChecked} on:click|stopPropagation={handleClick}>  {questionText}<br>

    {#if questionChecked }
        {#each items as item}
            <div style="display:flex; align-items:flex-end ; height:100%; width:100%;">
                <p style="width:100%">{item.name}</p>
                <button on:click={() => {handleDelete(item.id)}}>X</button>
            </div>
        {/each}

        <div style="display:flex; height:100%;">
            <input type="text" placeholder="{placeholderText}" bind:value={inputText}><br>
            <button on:click={handleAdd} >&plus;</button>
        </div>     
    {/if}

</form>


<style>

</style>