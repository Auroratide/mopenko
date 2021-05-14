<script>
    import { draw } from 'svelte/transition'
    import { Phase } from './Phase'

    const DRAG_FACTOR = 2
    const DRAG_SNAP_THRESHOLD = 135
    const CIRCLE_DELAY_START = 0
    const CIRCLE_DELAY_INTERVAL = 100

    let phase = Phase.Initial
    let dragging = false
    let startPosition = {
        x: 0, y: 0
    }
    let rotation = 0

    const startDrag = (e) => {
        if (phase === Phase.Initial) {
            dragging = true
            startPosition = {
                x: e.clientX, y: e.clientY
            }
        }
    }

    const endDrag = () => {
        if (dragging) {
            dragging = false
            if (rotation > DRAG_SNAP_THRESHOLD) {
                rotation = 180
                phase = Phase.Snapped
            } else {
                rotation = 0;
            }
        }
    }

    const drag = (e) => {
        if (dragging) {
            const dx = e.clientX - startPosition.x
            const dy = e.clientY - startPosition.y
            rotation = Math.sqrt(dx * dx + dy * dy) / DRAG_FACTOR

            if (rotation >= DRAG_SNAP_THRESHOLD)
                endDrag()
        }
    }
</script>

<svg
    on:mousedown={startDrag}
    on:mouseup={endDrag}
    on:mousemove={drag}
    xmlns="http://www.w3.org/2000/svg"
    viewBox="-500 -500 1000 1000"
>
    <g class="symbol" class:locked={phase === Phase.Snapped}>
        <path class="bottom" d="
            M -25 0
            A 25 25 0 0 1 25 0
            M 0 -25
            L 0 -100
            A 100 100 0 0 1 0 100
        " />

        <path class="top" class:dragging style="transform: rotate({rotation}deg);" d="
            M -25 0
            A 25 25 0 0 1 25 0
            M 0 -25
            L 0 -100
            A 100 100 0 0 1 0 100
        " />
    </g>

    {#if phase > Phase.Initial}
        <g>
            <circle in:draw={{delay: CIRCLE_DELAY_START + 1 * CIRCLE_DELAY_INTERVAL}} cx="0" cy="-400" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 4 * CIRCLE_DELAY_INTERVAL}} cx="259" cy="-305" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 6 * CIRCLE_DELAY_INTERVAL}} cx="394" cy="-67" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 2 * CIRCLE_DELAY_INTERVAL}} cx="343" cy="205" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 5 * CIRCLE_DELAY_INTERVAL}} cx="132" cy="378" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 3 * CIRCLE_DELAY_INTERVAL}} cx="-132" cy="378" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 0 * CIRCLE_DELAY_INTERVAL}} cx="-343" cy="205" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 8 * CIRCLE_DELAY_INTERVAL}} cx="-394" cy="-67" r="40" />
            <circle in:draw={{delay: CIRCLE_DELAY_START + 7 * CIRCLE_DELAY_INTERVAL}} cx="-259" cy="-305" r="40" />
        </g>
    {/if}
</svg>

<style>
    svg {
        width: 100%;
        height: 100%;
        background-image: url("/assets/parchment.jpg");
    }

    .top {
        transition: transform 400ms ease-in-out;
    }

    .top.dragging {
        transition: none;
    }

    path, circle {
        stroke: black;
        stroke-width: 10;
        fill: none;
    }

    .symbol {
        transition: transform 1400ms ease-in-out;
    }

    .symbol.locked {
        transform: rotate(810deg);
    }
</style>