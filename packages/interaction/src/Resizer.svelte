<svelte:options runes={true} />
<script>
    import { createBubbler } from 'svelte/legacy';

    const bubble = createBubbler();
    import {getContext} from 'svelte';
    import {bgEvent, helperEvent} from '@event-calendar/core';

    let { event } = $props();

    let {theme, eventDurationEditable, editable} = getContext('state');

    let resizable = $derived(!bgEvent(event.display) &&
        !helperEvent(event.display) && (
            (event.durationEditable ?? $eventDurationEditable) ||
            (event.editable ?? $editable)
        ));
    
</script>

{#if resizable}
    <div
        class="{$theme.resizer}"
        onpointerdown={bubble('pointerdown')}
    ></div>
{/if}