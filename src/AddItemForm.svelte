<script>
    export let placeholderText;
    import { createEventDispatcher } from 'svelte';
    export let items = [];
    //export let questionChecked = true;
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


<form style="align-items:stretch; margin 0px; font-size:12px">
    {#each items as item}
        <div style="display:flex; align-items:center ; width:100%; background-color: lightgrey; margin-top: 5px">
            <p style="width:100%; text-align:left; padding-left: 5px;">{item.name}</p>
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <span style="width:30px;" on:click={() => {handleDelete(item.id)}}>&minus;</span>
        </div>
    {/each}

    <div style="display:flex; align-items:center ; height:100%; width:100%; margin-top: 5px; background-color: lightgrey;">
        <input style="width:100%; margin: 5px;" type="text" placeholder="{placeholderText}" bind:value={inputText}><br>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span style="width:30px;" on:click={handleAdd} >&plus;</span>
    </div>      
</form>


<style>

</style>