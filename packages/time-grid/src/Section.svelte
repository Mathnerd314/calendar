<svelte:options runes={true} />
<script>
    import {getContext} from 'svelte';
    import {setContent} from '@event-calendar/core';
    import {createAllDayContent} from './utils.js';
    /**
     * @typedef {Object} Props
     * @property {import('svelte').Snippet} [lines]
     * @property {import('svelte').Snippet} [children]
     */

    /** @type {Props} */
    let { lines, children } = $props();

    let {allDayContent, theme, _times} = getContext('state');

    let allDayText = $derived(createAllDayContent($allDayContent));
    
</script>

<div class="{$theme.sidebar}">
    <div class="{$theme.sidebarTitle}" use:setContent={allDayText}></div>
    {#each $_times as time}
        <time class="{$theme.time}" datetime="{time[0]}" use:setContent={time[2] ? time[1] : ''}></time>
    {/each}
</div>
<div class="{$theme.days}" role="row">
    <div class="{$theme.lines}">{@render lines?.()}</div>
    {@render children?.()}
</div>
