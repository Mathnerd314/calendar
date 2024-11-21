<svelte:options runes={true} />
<script>
    import { run } from 'svelte/legacy';

    import {getContext} from 'svelte';
    import {
        addDay,
        cloneDate,
        createEventChunk,
        datesEqual,
        sortEventChunks,
        eventIntersects,
        setContent,
        setPayload,
        bgEvent,
        toISOString
    } from '@event-calendar/core';
    import Event from './Event.svelte';

    let { date } = $props();

    let {_events, _interaction, _intlListDay, _intlListDaySide, _today,
        resources, filterEventsWithResources, highlightedDates, theme} = getContext('state');

    let el = $state();
    let chunks = $state();
    let isToday = $derived(datesEqual(date, $_today)), highlight = $derived($highlightedDates.some(d => datesEqual(d, date)));
    let datetime = $derived(toISOString(date, 10));

    run(() => {
        chunks = [];
        let start = date;
        let end = addDay(cloneDate(date));
        for (let event of $_events) {
            if (!bgEvent(event.display) && eventIntersects(event, start, end, $filterEventsWithResources ? $resources : undefined)) {
                let chunk = createEventChunk(event, start, end);
                chunks.push(chunk);
            }
        }
        sortEventChunks(chunks);
    });

    
    
    

    // dateFromPoint
    run(() => {
        if (el) {
            setPayload(el, () => ({allDay: true, date, resource: undefined, dayEl: el}));
        }
    });
</script>

{#if chunks.length}
    <div
        bind:this={el}
        class="{$theme.day} {$theme.weekdays?.[date.getUTCDay()]}{isToday ? ' ' + $theme.today : ''}{highlight ? ' ' + $theme.highlight : ''}"
        role="listitem"
        onpointerdown={$_interaction.action?.select}
    >
        <h4 class="{$theme.dayHead}">
            <time {datetime} use:setContent={$_intlListDay.format(date)}></time>
            <time class="{$theme.daySide}" {datetime} use:setContent={$_intlListDaySide.format(date)}></time>
        </h4>
        {#each chunks as chunk (chunk.event)}
            <Event {chunk}/>
        {/each}
    </div>
{/if}