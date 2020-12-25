<script context="module">
  import { setContext, getContext, hasContext } from 'svelte'

  export const contextKey = {}
  export const today = new Date()
  today.setHours(0)
  today.setMinutes(0)
  today.setSeconds(0)
  today.setMilliseconds(0)

  export function calendarContext() {
    return getContext(contextKey)
  }
</script>

<script>
  import { onMount, createEventDispatcher } from 'svelte'
  import { writable } from 'svelte/store'
  import Month from './month.svelte'
  import Header from './header.svelte'
  import Options from './options.svelte'

  const dispatch = createEventDispatcher()
  let index = 0

  export let initial = new Date(today)
  export let selected
  export let disablePredicate = (date) => false
  export let locale = 'en-US'
  export let style = {}

  let x = 96
  let context = calendarContext()
  !hasContext(contextKey) ? setContext(contextKey, context = writable({ selected, initial })) : $context.calendar = initial

  // setContext(contextKey, context)

  onMount(function () {
    document.addEventListener('keyup', function (e) {
      if (e.code == 37) {
        previous()
      } else if (e.code == 39) {
        next()
      }
    })
  })

  function init(node) {}
</script>

<div class="calendar" on:click|stopPropagation="{() => {}}">
  <Header bind:index locale="{locale}" />
  <div class="container">
    <Month
    disablePredicate="{disablePredicate}"
      on:action="{function (_e) {
        selected = $context.selected
        dispatch('action', selected)
      }}"
    />
    {#if index == 1}
      <Options
        on:action="{() => {
          index = 0
        }}"
      />
    {/if}
  </div>
</div>

<style>
  :global(.disabled) {
    pointer-events: none;
    color: #dddddd !important;
  }
  .calendar {
    --border-radius: 0.1em;

    padding: 0.6em;
    width: 320px;
    height: 380px;
    box-shadow: 0 0 0 1px #dddddd;
    background-color: var(--background);
    box-sizing: border-box;
    display: inline-flex;
    flex-direction: column;
    border-radius: var(--border-radius);
    overflow: hidden;
  }
  .container {
    position: relative;
    flex: 1;
  }
  :global(.container > *) {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    width: 100%;
    height: 100%;
  }
</style>
