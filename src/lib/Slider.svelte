<script>
    // import { flip } from 'svelte/animate';
    import { tweened } from 'svelte/motion';

    let current = 1;
    let container;
    let panels = [
        {
            id: 1,
            el: null,
            name: 'foo',
            body: 'Lorem ipsum dolor sit amet.'
        },
        {
            id: 2,
            el: null,
            name: 'bar',
            body: 'Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.'
        },
        {
            id: 3,
            el: null,
            name: 'baz',
            body: 'Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.'
        }
    ];

    let trackLeft = tweened(0, {
        duration: 600
    });
    let minHeight = tweened(100, {
        duration: 600
    });

    $: width = container ? container.offsetWidth : 0;
    $: trackWidth =
        container && panels.length ? container.offsetWidth * panels.length : 0;
    $: {
        let floor = (panels.length - 1) * width * -1;
        let newleft = current > 0 && width > 0 ? (current - 1) * width * -1 : 0;

        trackLeft.set(Math.max(newleft, floor));

        let currentPanel =
            current > 0
                ? panels.filter(panel => panel.id === current)[0]
                : false;
        if (currentPanel && currentPanel.el !== null) {
            minHeight.set(currentPanel.el.offsetHeight);
        }
    }

    function goForward() {
        current = Math.min(current + 1, panels.length);
    }
    function goBackward() {
        current = Math.max(current - 1, 0);
    }
</script>

<div class="flex flex-col gap-2 mb-12">
    <div class="flex w-full gap-2">
        <button class="flex-initial text-sm font-bold" on:click={goBackward}
            >Prev</button
        >
        <input
            type="number"
            name="current"
            class="flex-grow w-full p-1 bg-gray-100 rounded"
            bind:value={current}
        />
        <button class="flex-initial text-sm font-bold" on:click={goForward}
            >Next</button
        >
    </div>
    <table class="text-xs text-center">
        <tr>
            <td>Current Slide<br />{current}</td>
            <td>Width<br />{width}</td>
            <td>Track Left Pos<br />{$trackLeft}</td>
            <td>Min Height<br />{$minHeight}</td>
        </tr>
    </table>
</div>
<div
    bind:this={container}
    class="relative w-1/3 mx-auto bg-gray-800 border-dashed border-green-500 border-2"
    style:min-height={`${$minHeight}px`}
>
    <div
        class="absolute top-0 flex items-start justify-start"
        style:width={`${trackWidth}px`}
        style:left={`${$trackLeft}px`}
    >
        {#each panels as thing, index (thing.id)}
            <div
                bind:this={panels[index].el}
                class="p-1 bg-green-600/20"
                style:width={`${width}px`}
            >
                <p>{thing.body}</p>
            </div>
        {/each}
    </div>
</div>

<style>
</style>
