<script>
    import { each } from "svelte/internal";
    import { createEventDispatcher } from 'svelte';

    export let tabItems;
    export let currentItem;

    let dispatch = createEventDispatcher();

</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class="tabs" on:click={(e) => dispatch('updateDisplay')}>
    <ul>
        {#each tabItems as item}
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <li on:click={(e) => dispatch('tabChange', item)}>
                <div class:active={item === currentItem}>{item}</div>
            </li>
        {/each}
    </ul>
</div>

<style>

    .tabs{
        margin-bottom: 40px;
    }

    ul{
        display: flex;
        justify-content: center;
        padding: 0;
        list-style-type: none;
    }

    li{
        margin: 0 16px;
        font-size: 18px;
        color: #9c9999;
        cursor: pointer;
    }

    .active{
        color: #303254;
        border-bottom: 2px solid #303254;
        padding-bottom: 3px;
    }

</style>