<script>
    import Icon from "../Icon/Icon.svelte";
    import transformModal from "./utils/transform-modal.js";
    import {onDestroy, onMount} from "svelte";

    export let className = ""
    export let styles = ""
    export let disabled = false
    export let hideArrow = false
    export let onOpen = () => null
    export let onClose = () => null
    let open = false
    let modal
    let button
    $: modal && button ? transformModal(open, modal, button) : null


    function handler(event) {
        const el = document.elementsFromPoint(event.clientX, event.clientY)
        let bClicked = false, mClicked = false
        el.forEach(element => {
            if (element === button)
                bClicked = true
            if (element === modal)
                mClicked = true
        })
        if (!bClicked && !mClicked) {
            if (onClose && open)
                onClose()
            open = false
            modal.style.display = "none"
        }
    }

    onMount(() => {
        document.addEventListener("mousedown", handler)
    })
    onDestroy(() => {
        document.removeEventListener("mousedown", handler)
    })
</script>

<div>
    <button
            bind:this={button}
            class="button {className}"

            on:click={() => {
            if(!open){
                if(onOpen)
                    onOpen()
                open = true
            }
        }}
            style={(hideArrow ? "" : "display: flex; align-items: center; gap: 4px; ") + styles}
            disabled="{disabled}"
    >
        {#if !hideArrow}
            <Icon styles={`${!open ? "transform: rotate(-90deg)" : ""}`}>arrow_drop_down</Icon>
        {/if}
        <slot name="header"/>
    </button>
    <div
        class="modal"
        bind:this={modal}
    >
        <slot/>
    </div>
</div>

<style>

    .modal {
        display: none;
        position: fixed;

        color: var(--pj-color-secondary);
        box-shadow: var(--pj-boxshadow);
        border-radius: 5px;
        background-color: var(--pj-background-tertiary);
        padding: 4px 8px;
        border: var(--pj-border-primary) 1px solid;
        min-width: 250px;
    }
</style>