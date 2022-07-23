<script>
    import {onMount, onDestroy} from 'svelte';
    import hsvToHsl from "./utils/hsv-to-hsl.js";

    export let submit = () => null
    export let size = 250


    let hue = 0, saturation = 0, value = 100
    let focused = false, boundingBox = {x: 0, y: 0}, clicked = false
    let picker, canvas

    function updatePicker() {
        const {h, s, l} = hsvToHsl(hue, saturation, value)
        picker.style.backgroundColor = `hsl(${h}, ${s}%, ${l}%)`
    }

    function handler(event) {
        switch (event.type) {
            case "mousemove": {
                if (focused || clicked) {
                    const x = event.clientX - boundingBox.x
                    const y = event.clientY - boundingBox.y
                    const percentageX = x / size
                    const percentageY = 1 - y / size

                    saturation = percentageX * 100
                    if (saturation > 100)
                        saturation = 100
                    if (saturation < 100 && saturation > 0)
                        picker.style.left = x + "px"

                    value = percentageY * 100
                    if (value > 100)
                        value = 100
                    if (value < 100 && value > 0)
                        picker.style.top = y + "px"
                    if (value <= 100 || saturation <= 100)
                        updatePicker()
                }
                break
            }
            case "mouseenter":
                boundingBox = canvas.getBoundingClientRect()
                break
            case "mousedown":
                clicked = true
                break
            case "mouseup":
                clicked = false
                submit(hsvToHsl(hue, saturation, value))
                break
        }

    }

    onDestroy(() => {
        canvas.removeEventListener("mousemove", handler)
        canvas.removeEventListener("mouseenter", handler)
        canvas.removeEventListener("mousedown", handler)
        document.body.removeEventListener("mouseup", handler)
    })
    onMount(() => {
        picker = document.querySelector(".picker")
        console.log(picker)
        updatePicker()
        canvas = document.querySelector(".canvas")
        canvas.addEventListener("mousemove", handler)
        canvas.addEventListener("mouseenter", handler)
        canvas.addEventListener("mousedown", handler)
        document.body.addEventListener("mouseup", handler)
    })
</script>

<style>
    .container {
        box-sizing: border-box;
        display: grid;
        justify-items: center;
        align-content: center;
        gap: 16px;
        background-color: #f4f5f5;
        padding: 16px;
        --hue: 0;
        --box-shadow: 0 0 0 1px rgba(0, 0, 0, .025), 0 1px 5px rgba(0, 0, 0, 0.25);
    }

    input {
        width: 100%;
    }

    input[type='range']::-webkit-slider-runnable-track {
        height: 6px;
        color: #13bba4;
        background: linear-gradient(
                to right,
                #ff0000 0%,
                #ffff00 17%,
                #00ff00 33%,
                #00ffff 50%,
                #0000ff 67%,
                #ff00ff 83%,
                #ff0000 100%
        );
    }

    input[type=range]::-webkit-slider-thumb {
        -webkit-appearance: none;
    }

    input[type=range] {
        -webkit-appearance: none;
    }

    input[type=range]::-webkit-slider-thumb {
        -webkit-appearance: none;
        cursor: ew-resize;
        background-color: hsl(var(--hue), 100%, 50%) !important;
        border: 4px solid white;
        width: 20px;
        height: 20px;
        border-radius: 50%;
        box-shadow: var(--box-shadow);
        transform: translateY(-7px);
    }

    input[type=range]::-webkit-slider-thumb:active {
        transform: translateY(-7px) scale(1.1);
    }

    .canvas {
        background-image: linear-gradient(0deg, #000, transparent), linear-gradient(90deg, #fff, hsla(0, 0%, 100%, 0));
        background-color: hsl(var(--hue), 100%, 50%);
        position: relative;
        overflow: hidden;
        border-radius: 5px;
    }

    .picker {
        width: 25px;
        height: 25px;
        border-radius: 50px;
        position: fixed;

        top: 0;
        left: 0;
        z-index: 99999;
        border: #e0e0e0 2px solid;
        box-shadow: var(--box-shadow);

    }

</style>


<div class="container" style="--hue: {hue}; width: {size + 32}px; height:  {size + 48}px; ">
    <div style="width: {size}px; height: {size}px" class="canvas">
        <div class="picker"></div>
    </div>
    <input
            on:input={(event) => {
            hue= parseFloat(event.target.value)
            event.target.parentElement.style.setProperty('--hue', event.target.value);
            updatePicker()
        }}
            type="range"
            value={hue}
            max="360"
    >
</div>