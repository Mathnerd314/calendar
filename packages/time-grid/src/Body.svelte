<svelte:options runes={true} />
<script>
    import { run } from 'svelte/legacy';

    import {getContext} from 'svelte';
    import Section from './Section.svelte';
    /**
     * @typedef {Object} Props
     * @property {import('svelte').Snippet} [children]
     */

    /** @type {Props} */
    let { children } = $props();

    let {_bodyEl, _viewDates, _slotTimeLimits, _times, scrollTime, slotDuration, slotHeight, theme} = getContext('state');

    let el = $state();
    let compact = $state();
    let lines = $state([]);




    function scrollToTime() {
        el.scrollTop = (($scrollTime.seconds - $_slotTimeLimits.min.seconds) / $slotDuration.seconds - 0.5) * $slotHeight;
    }
    run(() => {
        $_bodyEl = el;
    });
    run(() => {
        compact = $slotDuration.seconds >= 3600;
        lines.length = $_times.length;
    });
    run(() => {
        if (el) {
            $_viewDates;
            $scrollTime;
            scrollToTime()
        }
    });
</script>

<div
    bind:this={el}
    class="{$theme.body}{compact ? ' ' + $theme.compact : ''}"
>
    <div class="{$theme.content}">
        <Section>
            {#snippet lines()}
                    
                    {#each lines as line}
                        <div class="{$theme.line}"></div>
                    {/each}
                
                    {/snippet}
            {@render children?.()}
        </Section>
    </div>
</div>
