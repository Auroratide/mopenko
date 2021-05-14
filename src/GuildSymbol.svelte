<script>
    let dragging = false
    let startPosition = {
        x: 0, y: 0
    }
    let rotation = 0

    const startDrag = (e) => {
        dragging = true
        startPosition = {
            x: e.clientX, y: e.clientY
        }
    }

    const endDrag = () => {
        dragging = false
        rotation = 0;
    }

    const drag = (e) => {
        if (dragging) {
            const dx = e.clientX - startPosition.x
            const dy = e.clientY - startPosition.y
            rotation = Math.sqrt(dx * dx + dy * dy)
        }
    }
</script>

<svg
    on:mousedown={startDrag}
    on:mouseup={endDrag}
    on:mousemove={drag}
    xmlns="http://www.w3.org/2000/svg"
    viewBox="-110 -110 220 220"
>
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
</svg>

<style>
    svg {
        width: 100%;
        height: 100%;
    }

    .top {
        transition: transform 400ms ease-in-out;
    }

    .top.dragging {
        transition: none;
    }

    path {
        stroke: black;
        stroke-width: 10;
        fill: none;
    }
</style>