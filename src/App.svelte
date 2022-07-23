<script>
    import ColorPicker from "./components/color-picker/ColorPicker.svelte";
    import Input from "./components/input/Input.svelte";
    import Icon from "./components/Icon/Icon.svelte";
    let color = {h: 0, s: 0, l: 0}
    let realtime = false
</script>

<style>
    .container {
        display: grid;
        gap: 7px;
        justify-items: center;
        padding: 8px;
        margin-bottom: 16px;
    }
</style>
<div class="container">
    <label>Color picker</label>
    <ColorPicker handleChange={(v) => color = v} realtime={realtime}/>
    <nav>H: {Math.round(color.h)} | S: {Math.round(color.s)} | L: {Math.round(color.l)}</nav>
    <button on:click={() => realtime = !realtime}>Toggle real-time ({realtime ? "yes" : "no"})</button>
</div>
<div class="container">
    <label>Input com debounce</label>
    <Input setSearchString={v => console.log(v)} placeholder="Digite aqui e veja o console"/>
</div>
<div class="container">
    <label>Icons</label>
    <nav>
        <Icon>open_in_new</Icon>
        <Icon>person</Icon>
        <Icon>category</Icon>
        <Icon>close</Icon>
    </nav>
</div>