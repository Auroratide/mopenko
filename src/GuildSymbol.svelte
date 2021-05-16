<script>
    import { blur, fly, draw } from 'svelte/transition'
    import { Phase } from './Phase'
    import { Stone } from './Stone'
    import PokedexEntry from './PokedexEntry.svelte'

    const DRAG_FACTOR = 1
    const DRAG_SNAP_THRESHOLD = 135
    const CIRCLE_DELAY_START = 0
    const CIRCLE_DELAY_INTERVAL = 100

    let phase = Phase.Initial
    let dragging = false
    let startPosition = {
        x: 0, y: 0
    }
    let rotation = 0
    let stonesActivated = {}
    let showPokedexEntry = null
    let showCarnot = false

    const activateCarnot = (e) => {
        console.log(e.key)
        if (e.key === 'g') {
            showPokedexEntry = null
            setTimeout(() => showCarnot = true, 800)
        }
    }

    $: {
        if (phase === Phase.Snapped) {
            setTimeout(() => phase = Phase.Mapped, 1000)
        } else if (phase === Phase.Mapped) {
            setTimeout(() => phase = Phase.Revealed, 1500)
        } else if (phase === Phase.Revealed) {
            document.body.addEventListener('keyup', activateCarnot)
        }
    }

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

    const activateStone = (stone) => {
        stonesActivated = Object.assign({}, stonesActivated, {
            [stone]: true
        })

        setTimeout(() => showPokedexEntry = stone, 2000)
    }

    const clickStone = (stone) => () => {
        showCarnot = false
        if (showPokedexEntry !== null) {
            showPokedexEntry = null
            setTimeout(() => activateStone(stone), 400)
        } else {
            activateStone(stone)
        }
    }
</script>

<div class="preloading">
    <img class="bg" src="/assets/parchment.jpg" alt="parchment" />
    <img class="bg" src="/assets/landmarks.jpg" alt="landmarks" />
    <img src="/assets/spiritomb.jpg" alt="spiritomb" />
    <img src="/assets/swalot.jpg" alt="swallot" />
    <img src="/assets/venomoth.png" alt="venomoth" />
    <img src="/assets/oddish.jpg" alt="oddish" />
    <img src="/assets/durant.png" alt="durant" />
    <img src="/assets/drednaw.jpg" alt="drednaw" />
    <img src="/assets/politoed.jpg" alt="politoed" />
</div>
<article class="container" class:snapped={phase === Phase.Snapped}>
    {#if phase < Phase.Mapped}
        <img transition:blur={{duration: 1500}} class="bg" src="/assets/parchment.jpg" alt="parchment" />
    {/if}
    {#if phase >= Phase.Mapped}
        <img transition:blur={{duration: 1500}} class="bg" src="/assets/landmarks.jpg" alt="landmarks" />
    {/if}
    <svg
        on:mousedown={startDrag}
        on:mouseup={endDrag}
        on:mousemove={drag}
        xmlns="http://www.w3.org/2000/svg"
        viewBox="-500 -500 1000 1000"
    >
        <g class="symbol" class:snapped={phase >= Phase.Snapped} class:revealed={phase >= Phase.Revealed}>
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

        {#if phase >= Phase.Revealed}
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
    {#if phase >= Phase.Revealed}
        <div in:fly={{ y: 66, delay: 2500 }} class="stones">
            <img class:activated={stonesActivated[Stone.Dusk]} on:click={clickStone(Stone.Dusk)} id="dusk-stone" src="/assets/dusk-stone.png" alt="Dusk Stone" />
            <img class:activated={stonesActivated[Stone.Sol]} on:click={clickStone(Stone.Sol)} id="sol-stone" src="/assets/sol-stone.png" alt="Sol Stone" />
            <img class:activated={stonesActivated[Stone.Leaf]} on:click={clickStone(Stone.Leaf)} id="leaf-stone" src="/assets/leaf-stone.png" alt="Leaf Stone" />
            <img class:activated={stonesActivated[Stone.Ice]} on:click={clickStone(Stone.Ice)} id="ice-stone" src="/assets/ice-stone.png" alt="Ice Stone" />
            <img class:activated={stonesActivated[Stone.Thunder]} on:click={clickStone(Stone.Thunder)} id="thunder-stone" src="/assets/thunder-stone.png" alt="Thunder Stone" />
            <img class:activated={stonesActivated[Stone.Dawn]} on:click={clickStone(Stone.Dawn)} id="dawn-stone" src="/assets/dawn-stone.png" alt="Dawn Stone" />
        </div>
    {/if}
    <section class="pokedex-entries">
        {#if showCarnot}
            <PokedexEntry name="Politoed" text="Nyorotono, the Frog Kaijin. Nyorotono likes to expand its throat and sing out, drawing in Nyoromo and Nyorozo from all around." />
        {:else if showPokedexEntry === Stone.Sol}
            <PokedexEntry name="Spiritomb" text="Mikaruge, the Forbidden Kaijin. As punishment for misdeeds, it was imprisoned in the fissure of an Odd Keystone." />
        {:else if showPokedexEntry === Stone.Ice}
            <PokedexEntry name="Swalot" text="Marunoom, the Poison Bag Kaijin. Marunoom has a body that can expand and contract at will, and can swallow anything whole." />
        {:else if showPokedexEntry === Stone.Dusk}
            <PokedexEntry name="Venomoth" text="Morphon, an evolved form of Kongpang. Tiny scales on its wings disperse various spores when they are flapped." ext="png" />
        {:else if showPokedexEntry === Stone.Leaf}
            <PokedexEntry name="Oddish" text="Nazonokusa. This Kaijin is typically found roaming the forest, scattering rafureshia as it walks around." />
        {:else if showPokedexEntry === Stone.Thunder}
            <PokedexEntry name="Durant" text="Aiant, the Iron Ant Kaijin. Aiant build twisting tunnels in the mountains where they form their nests. They are covered with a steel armor for protection." ext="png" />
        {:else if showPokedexEntry === Stone.Dawn}
            <PokedexEntry name="Drednaw" text="Kajirigame, the Bite Kaijin. With jaws that can shear through steel rods, this highly aggressive Kaijin chomps down on its unfortunate prey." />
        {/if}
    </section>
</article>

<style>
    .container {
        position: relative;
        --color-etching: 0, 0, 0;
        --color-power: 78, 171, 248;
    }

    .container.snapped {
        animation: shaky-cam 400ms linear 128ms;
    }

    svg {
        width: 100%;
        height: 100%;
        position: relative;
        z-index: 1;
    }

    .bg {
        width: 100%;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 0;
        opacity: 0.667;
    }

    .preloading {
        opacity: 0;
        width: 0;
        height: 0;
        position: absolute;
        top: 0;
        left: 0;
    }

    .top {
        transition: transform 128ms ease-out;
    }

    .top.dragging {
        transition: none;
    }

    .symbol {
        transition: transform 1800ms ease-in-out;
    }

    .symbol.snapped {
        animation: shadow-burst 1000ms ease-out 500ms;
        animation-fill-mode: both;
    }

    .symbol.revealed {
        transform: rotate(810deg);
    }

    path, circle {
        stroke: rgba(var(--color-etching), 1);
        stroke-width: 10;
        fill: none;
    }

    .stones {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 3;
    }

    .stones img {
        position: absolute;
        max-width: 12vmin;
        transition: top 1800ms ease-in-out, left 1800ms ease-in-out, max-width 1800ms linear;
        animation: ambient-float 8s ease-in-out;
        animation-iteration-count: infinite;
    }

    .stones .activated {
        max-width: 8vmin;
        animation: none;
    }

    #dusk-stone {
        top: 5vmin;
        left: -20vmin;
    }

    #dusk-stone.activated {
        top: 38vmin;
        left: 85.5vmin;
    }

    #sol-stone {
        top: 40vmin;
        left: -20vmin;
        animation-delay: -1s;
    }

    #sol-stone.activated {
        top: 15.5vmin;
        left: 20vmin;
    }

    #leaf-stone {
        top: 70vmin;
        left: -20vmin;
        animation-delay: -2s;
    }

    #leaf-stone.activated {
        top: 65vmin;
        left: 80.5vmin;
    }

    #ice-stone {
        top: 5vmin;
        left: 108vmin;
        animation-delay: -0.5s;
    }

    #ice-stone.activated {
        top: 84vmin;
        left: 59vmin;
    }

    #thunder-stone {
        top: 40vmin;
        left: 108vmin;
        animation-delay: -2.5s;
    }

    #thunder-stone.activated {
        top: 5.5vmin;
        left: 46vmin;
    }

    #dawn-stone {
        top: 70vmin;
        left: 108vmin;
        animation-delay: -1.5s;
    }

    #dawn-stone.activated {
        top: 39.5vmin;
        left: 7vmin;
    }

    .pokedex-entries {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 4;
        width: 100%;
    }

    @keyframes shadow-burst {
        from {
            filter: drop-shadow(0 0 0 rgba(var(--color-power), 1));
        }

        to {
            filter: drop-shadow(0 0 1em rgba(var(--color-power), 0.667));
        }
    }

    @keyframes shaky-cam {
        0% { top: -0.75%; left: 0%; }
        10% { top: 0.33%; left: -0.33%; }
        20% { top: 1%; left: 0.5%; }
        30% { top: 0.18%; left: -0.6%; }
        40% { top: 0.6%; left: 0.05%; }
        50% { top: 0.8%; left: -0.9%; }
        60% { top: 0.1%; left: -0.1%; }
        70% { top: 0.6%; left: 0%; }
        80% { top: -0.4%; left: 0.6%; }
        90% { top: -0.1%; left: -0.3%; }
        100% { top: 0%; left: 0%; }
    }

    @keyframes ambient-float {
        0% { transform: translate(0, 0); }
        33% { transform: translate(0, 1vmin); }
        66% { transform: translate(0, -0.5vmin); }
        100% { transform: translate(0, 0); }
    }
</style>