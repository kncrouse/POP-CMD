<script>
    import { createEventDispatcher } from 'svelte';

    export let diagramElements = [];
    export let lifehistoryElements = [];
    export let actionElements = []

    let firstElement;
    let secondElement;
    let elementConnection;

    let dispatch = createEventDispatcher();

    const handleAdd = () => {
        const item = {
            firstElement,
            secondElement,
            elementConnection,
            id: Math.random()
        }
        firstElement = '';
        secondElement = '';
        elementConnection = '';
        //diagramElements = [...diagramElements, item]
        dispatch('addItem', item)
    }

    const handleDelete = (id) => {
        //diagramElements = diagramElements.filter((item) => item.id != id)
        dispatch('deleteItem', id)
    }

</script>


<form style="align-items:center; margin:10px; justify-content:center; ">

    {#each diagramElements as element}
        <div style="display:flex; height:100%; width: 100%">
            <p>{element.firstElement} -- {element.elementConnection} --> {element.secondElement}</p>
            <button on:click={() => {handleDelete(element.id)}}>X</button>
        </div>
    {/each}

    <div style="display:flex; height:100%; ">
        <select bind:value={firstElement} style="min-width: 200;" placeholder="Life History">
            {#each lifehistoryElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
		</select>
        <p>--</p>
        <select bind:value={elementConnection} style="min-width: 200;" placeholder="Function">
            {#each actionElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
		</select>
        <p>--></p>
        <select bind:value={secondElement} style="min-width: 200;" placeholder="Life History">
            {#each lifehistoryElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
		</select>
        <button on:click|preventDefault={handleAdd} >&plus;</button>
    </div>     

</form>


<style>

</style>