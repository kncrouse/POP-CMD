<script>
    export let heading;
    export let headingColor;
    export let categoryIncluded = false;
    let isOpen = false;
</script>

<button class:active={isOpen} class:unused={!categoryIncluded} class="accordion" on:click={() => isOpen = !isOpen} style:background-color={headingColor}>
    <span class="heading">{heading}</span>

    {#if isOpen}
        <span class="icon">&minus;</span>  
    {:else}
        <span class="icon">&plus;</span>
    {/if}

</button>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class="panel" class:open-panel={isOpen} style="align-items:left;" on:click|preventDefault>
    <slot></slot>
</div>

<style>
    /* Style the buttons that are used to open and close the accordion panel */
    .accordion {
        width: 100%;
        padding: 15px;
        margin: 0;
        display: flex;
        justify-content: space-between;
        align-items: left;
        text-align: left;
        background-color: #eee;
        color: #444;
        border: none;
        cursor: pointer;
        outline: none;
        transition: 0.4s;
    }

    /* Add a background color to the button if it is clicked on (add the .active class with JS), and when you move the mouse over it (hover) */
    button.accordion.active, button.accordion:hover {
        background-color: #ccc;
        color: white;
    }

    /* Style the accordion panel. Note: hidden by default */
    .panel {
        padding: 0 18px;
        background-color: white;
        display: block;
        height: 0;
        overflow: hidden;
    }

    .open-panel {
        height:fit-content;
    }

    .unused {
        opacity: 0.5;
    }

</style>