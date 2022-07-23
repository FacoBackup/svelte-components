<script>
    import Icon from "../Icon/Icon.svelte";
    import {onMount} from "svelte";

    const DELAY = 250, ENTER = "Enter"
    export let width
    export let height
    export let icon = false
    export let noPadding = false
    export let setSearchString = () => null
    export let searchString = ""
    export let noAutoSubmit = false
    export let placeholder = ""

    let timeout, input
    const onChange = (input) => {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
            setSearchString(input.value)
        }, DELAY)
    }
    const updateInput = (s=searchString) => {
        if(s)
            input.value= s
    }
    onMount(updateInput)
    $: updateInput(searchString)
</script>

<div
    class="wrapper"
    style={`"width: ${width} ${height ? ", height: " + height : ""} ${noPadding ? ", padding: 0" : ""}"`}
>
    {#if icon}
        {icon}
    {/if}
    <input
        placeholder={placeholder}
        class="input"
        bind:this={input}
        on:input={e => {
            if(!noAutoSubmit)
                onChange(e.target)
        }}
        on:keydown={e => {
            if(e.code === ENTER)
               setSearchString(e.target.value)
        }}
    />
</div>

<style>
    .wrapper {
        height: 23px;
        min-height: 23px;
        display: flex;
        align-items: center;
        gap: 4px;
        padding: 3px;
        overflow: hidden;
        border-radius: 3px;
        width: 100%;
        max-width: 250px;
        color: var(--pj-color-secondary);
        background: var(--pj-background-tertiary);
    }

    .input {
        font-size: .7rem;
        border-radius: 5px;
        border: none;
        background: transparent;
        outline: none;
        width: 100%;
        color: var(--pj-color-secondary);
    }
</style>