<script>
    // import { flip } from 'svelte/animate';
    import { tweened } from 'svelte/motion';

    let panels = [
            {
                el: null,
                name: 'foo',
                body: 'Lorem ipsum dolor sit amet.'
            },
            {
                el: null,
                name: 'bar',
                body: 'Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.'
            },
            {
                el: null,
                name: 'baz',
                body: 'Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.Lorem ipsum dolor sit amet.'
            }
        ],
        container,
        currentIndex = 0;

    $: containerWidth = container ? container.offsetWidth : 0;
    $: trackWidth = container ? container.offsetWidth * panels.length : 0;
    $: trackLeftFloor = container
        ? (panels.length - 1) * container.offsetWidth * -1
        : 0;
    $: currentPanel = currentIndex >= 0 ? panels[currentIndex] : false;

    let trackLeft = tweened(0, {
        duration: 600
    });
    let containerMinHeight = tweened(100, {
        duration: 600
    });
    let currentOpacity = tweened(1, {
        duration: 200
    });

    function goForward() {
        currentIndex = Math.min(currentIndex + 1, panels.length - 1);

        currentOpacity.set(0).then(() => {
            let newleft = currentIndex * containerWidth * -1;
            let newHeight =
                currentPanel && currentPanel.el !== null
                    ? currentPanel.el.offsetHeight
                    : 0;

            containerMinHeight.set(newHeight);

            trackLeft.set(Math.max(newleft, trackLeftFloor)).then(() => {
                currentOpacity.set(1);
            });
        });
    }
    function goBackward() {
        // fade out content
        // move track
        // fade in content
        currentIndex = Math.max(currentIndex - 1, 0);

        currentOpacity.set(0).then(() => {
            let newleft = currentIndex * containerWidth * -1;
            let newHeight =
                currentPanel && currentPanel.el !== null
                    ? currentPanel.el.offsetHeight
                    : 0;

            containerMinHeight.set(newHeight);

            trackLeft.set(Math.max(newleft, trackLeftFloor)).then(() => {
                currentOpacity.set(1);
            });
        });
    }
</script>

<div class="flex flex-col gap-2 mb-12">
    <div class="flex w-full justify-between gap-2">
        <button
            class="flex-initial text-sm font-bold"
            on:click={goBackward}
            disabled={currentIndex === 0}
        >
            Prev
        </button>
        <button
            class="flex-initial text-sm font-bold"
            on:click={goForward}
            disabled={currentIndex >= panels.length - 1}
        >
            Next
        </button>
    </div>
    <!-- <table class="text-xs text-center">
        <tr>
            <td>Current Slide<br />{currentIndex}</td>
            <td>Width<br />{containerWidth}</td>
            <td>Track Left Pos<br />{$trackLeft}</td>
            <td>Min Height<br />{$containerMinHeight}</td>
        </tr>
    </table> -->
</div>
<div
    bind:this={container}
    class="relative w-1/3 mx-auto bg-gray-800 border-dashed border-green-500 border-2"
    style:min-height={`${$containerMinHeight}px`}
>
    <div
        class="absolute top-0 flex items-start justify-start"
        style:width={`${trackWidth}px`}
        style:left={`${$trackLeft}px`}
    >
        {#each panels as thing, index (index)}
            <div
                bind:this={panels[index].el}
                class="p-1 bg-green-600/20"
                style:width={`${containerWidth}px`}
                style:opacity={`${$currentOpacity}`}
            >
                <p>{thing.body}</p>
            </div>
        {/each}
    </div>
</div>

<style>
</style>
