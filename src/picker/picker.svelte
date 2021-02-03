<script>
  import { createEventDispatcher } from 'svelte'
  import { fade } from 'svelte/transition'
  import { expoOut } from 'svelte/easing'
  import Calendar from '../calendar.svelte'
  import Transition from '../transition.svelte'
  import DateInput from './date_input.svelte'
  import CalendarIcon from '../icons/calendar_icon.svelte'
  import { getCalendarContext, setCalendarContext } from '../context.js'
  import { writable } from 'svelte/store'

  const dispatch = createEventDispatcher()
  const { selectedContext } = getCalendarContext({
    selectedContext: writable(undefined),
    calendarContext: writable()
  })

  let editable = false
  // export let style = ''
  // export let config = {
  //   format: '#{m}/#{d}/#{Y}',
  //   star: new Date(1987, 9, 29),
  //   end: new Date(2020, 9, 29),
  //   selected: undefined,
  //   weekStart: 0,
  //   theme: {
  //     buttonBackgroundColor: '#fff',
  //     buttonBorderColor: '#eee',
  //     buttonTextColor: '#333',
  //     highlightColor: '#f7901e',
  //     dayBackgroundColor: 'none',
  //     dayTextColor: '#4a4a4a',
  //     dayHighlightedBackgroundColor: '#efefef',
  //     dayHighlightedTextColor: '#4a4a4a'
  //   }
  // }
  export let selected
  export let locale = 'en'
  export let format = 'dd/MM/yyyy'
  export let position = 'auto'

  let open = false
  let translate = { x: 0, y: 0 }
  let windowsWidth

  selected && ($selectedContext = selected)

  function grow(node, { delay = 0, duration = 400, easing = expoOut }) {
    return {
      delay,
      duration,
      easing,
      css: (t, u) => {
        return `transform: scale(${t},${0.1 + t * 0.9});opacity: ${t * 3}`
      }
    }
  }

  function initContainer(node) {
    function focusLossHandler(event) {
      if (!open) return
      let element = event.currentTarget
      while (element) {
        if (element === node) return
        element = element.parentElement
      }
      open = false
    }
    function getDistanceToEdges() {
      let rect = node.getBoundingClientRect()
      return {
        top: rect.top + -1 * translate.y,
        bottom: window.innerHeight - rect.bottom + translate.y,
        left: rect.left + -1 * translate.x,
        right: document.body.clientWidth - rect.right + translate.x
      }
    }
    function getTranslate() {
      let dist = getDistanceToEdges()
      let x
      let y
      if (windowsWidth < 480) {
        y = dist.bottom
      } else if (dist.top < 0) {
        y = Math.abs(dist.top)
      } else if (dist.bottom < 0) {
        y = dist.bottom
      } else {
        y = 0
      }
      if (dist.left < 0) {
        x = Math.abs(dist.left)
      } else if (dist.right < 0) {
        x = dist.right
      } else {
        x = 0
      }
      return { x, y }
    }
    translate = getTranslate()

    document.addEventListener('click', focusLossHandler)

    return {
      destroy: () => {
        document.removeEventListener('click', focusLossHandler)
      }
    }
  }
  $: console.log(open)
</script>

<svelte:window bind:innerWidth="{windowsWidth}" />
<div
  class="picker"
  on:click|stopPropagation="{function () {
    open = true
  }}"
>
  {#if open}
    <div
      class="outer-container"
      style="transform:translate(-50%, -50%) translate({translate.x}px, {translate.y}px)!important;"
      use:initContainer
    >
      <Transition
        in="{[grow, { duration: 300, delay: 100 }]}"
        out="{[grow, { duration: 300 }]}"
        on:outroend="{() => dispatch('closed')}"
      >
        <Transition in="{[fade, { duration: 400 }]}">
          <slot close="{() => (open = false)}">
            <Calendar
              locale="{locale}"
              predicate="{(d) => false}"
              on:action="{function (e) {
                dispatch('selected')
                open = false
              }}"
            />
          </slot>
        </Transition>
      </Transition>
    </div>
  {/if}

  <div class="inner" class:disabled="{!editable}">
    <div>
      {#each [...format.matchAll(/(\w+|\W+)/g)] as f}
        {#if f[0].match(/\w+/g)}
          <DateInput date="{$selectedContext}" format="{f[0]}" />
        {:else}<span>{f[0]}</span>{/if}
      {/each}
    </div>
    <div class="icon">
      <CalendarIcon />
    </div>
  </div>
</div>

<style>
  :global(*) {
    user-select: none;
    --background: #fff;
    --color: rgb(32, 32, 32);
    --weekend-background: rgb(66, 141, 129);
    --weekend-color: rgb(175, 76, 76);
    --selected-background: var(--weekend-color);
    --selected-color: #fff;
    --today-background: var(--weekend-color);
    --today-color: rgb(160, 80, 80);
    --disabled-background: rgb(201, 201, 201);
    --disabled-color: rgb(226, 226, 226);
    --offmonth-background: #fff;
    --offmonth-color: rgb(184, 184, 184);
    --header-background: #fff;
    --header-color: #fff;
    --week-days-background: #fff;
    --week-days-color: #fff;

    --day-radius: 0;
  }
  .picker {
    border: 1.6px solid #e6e6e6;
    border-radius: 0.3em;
    position: relative;
    cursor: pointer;
    width: fit-content;
    padding: 0.3em;
  }
  .inner {
    min-width: fit-content;
    width: auto;
    padding-left: 0.4em;
    pointer-events: none;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .icon {
    font-size: 1.6em;
    padding: 0 8px;
    width: 1em;
    display: flex;
  }
  .outer-container {
    transform: translate(-50%, -50%);
    position: absolute;
    top: 50%;
    left: 50%;
    transition: none;
    z-index: 2;
    display: block;
  }
  :global(.inner-container) {
    background: #fff;
    box-shadow: 0px 4px 8px rgb(0 0 0 / 0.16);
    padding-top: 0;
  }
</style>
