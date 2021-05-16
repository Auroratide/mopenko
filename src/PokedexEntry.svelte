<script>
    import { onMount } from 'svelte'
    import { blur } from 'svelte/transition'
    export let name = ''
    export let text = ''
    export let ext = 'jpg'

    let currentText = ''

    onMount(() => {
        currentText = ''
        let i = 0
        text.split('').forEach(char => {
            setTimeout(() => currentText += char, i)
            let delay = 75
            if (char === ',' || char === ';')
                delay += 33
            else if (char === '.' || char === '?' || char === '!')
                delay += 100
            else if (char === ' ')
                delay = 33
            
            i += delay
        })
    })
</script>

<figure transition:blur>
    <img src="/assets/{name.toLowerCase()}.{ext}" alt="{name}" />
    <figcaption>{currentText}</figcaption>
</figure>

<style>
    figure {
        font-family: var(--pokedex-font);
        font-size: 3vmin;
        display: flex;
        justify-content: center;
        flex-direction: column;
    }

    img {
        box-sizing: border-box;
        max-width: 60vmin;
        display: block;
        margin: auto;
        border-radius: 50%;
        margin-bottom: 5vmin;
        animation: ambient-blur 10s linear;
        animation-iteration-count: infinite;
    }

    figcaption {
        text-align: center;
        background: black;
        color: white;
        box-shadow: 0 0 1vmin 2vmin black;
        border-radius: 2vmin / 1vmin;
    }

    @keyframes ambient-blur {
        0% { filter: blur(0); }
        10% { filter: blur(1px); }
        15% { filter: blur(2px); }
        25% { filter: blur(0.5px); }
        40% { filter: blur(1px); }
        50% { filter: blur(0); }
        55% { filter: blur(1px); }
        65% { filter: blur(2px); }
        80% { filter: blur(0.5px); }
        90% { filter: blur(0.667px); }
        100% { filter: blur(0); }
    }
</style>