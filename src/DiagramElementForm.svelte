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


<form style="align-items:center; margin:10px; justify-content:center; " >

    {#each diagramElements as element}
        <div style="display:flex; align-items:center ; width:100%; background-color:lightgoldenrodyellow; margin-top: 5px">
            <p style="width:100%; text-align:center; padding-left: 5px;">
                {element.firstElement}
                <img
                    src="images/right-arrow.png"
                    alt="Loading..."
                    style="height: 10px;"/>
                {element.elementConnection}
                <img
                    src="images/right-arrow.png"
                    alt="Loading..."
                    style="height: 10px;"/>
                {element.secondElement}
            </p>
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <span style="width:30px;" on:click|preventDefault={() => {handleDelete(element.id)}}>&minus;</span>
        </div>
    {/each}

    <div style="display:flex; height:100%; align-items:center ; background-color: lightgoldenrodyellow; margin-top: 5px; padding: 5px;">
        <select bind:value={firstElement} style="min-width: 200; width: 100%;" placeholder="Life History">
            {#each lifehistoryElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
		</select>
        <p style="width:80px"> 
            <img
            src="images/right-arrow.png"
            alt="Loading..."
            style="height: 10px;"/>
        </p>
        <select bind:value={elementConnection} style="min-width: 200; width: 100%;" placeholder="Function">
            {#each actionElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
            <option>    </option> ; option for blank text
		</select>
        <p style="width:80px">
            <img
            src="images/right-arrow.png"
            alt="Loading..."
            style="height: 10px;"/>
        </p>
        <select bind:value={secondElement} style="min-width: 200; width: 100%;" placeholder="Life History">
            {#each lifehistoryElements as element}
                <option value={element.name}>{element.name}</option>  
            {/each}
		</select>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span style="width:70px;"  on:click|preventDefault={handleAdd} >&plus;</span>
    </div>     

</form>

<style>

</style>