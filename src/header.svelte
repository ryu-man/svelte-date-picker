<script>
  import { format } from 'date-fns'
  import { getCalendarContext } from './context.js'
  import ArrowLeftIcon from './icons/arrow_left_icon.svelte'
  import ArrowRightIcon from './icons/arrow_right_icon.svelte'

  export let index = 0
  export let locale = 'en-US'

  const { calendarContext } = getCalendarContext()
  console.log($calendarContext)
  function next() {
    $calendarContext.setMonth($calendarContext.getMonth() + 1)
    $calendarContext = $calendarContext
  }

  function prev() {
    $calendarContext.setMonth($calendarContext.getMonth() - 1)
    $calendarContext = $calendarContext
  }
</script>

<div class="header">
  <div
    class="navigation button "
    role="button"
    on:click="{prev.call($calendarContext)}"
  >
    <ArrowLeftIcon />
  </div>
  <div
    class="text button"
    on:click="{function () {
      index = 1
    }}"
  >
    {format($calendarContext, 'MMMM yyyy')}
  </div>
  <div
    class="navigation button"
    role="button"
    on:click="{next.call($calendarContext)}"
  >
    <ArrowRightIcon />
  </div>
</div>

<style>

  .header {
    padding: 0.5em;
    padding-bottom: 1em;
    font-size: 1em !important;
    font-weight: 700;
    color: rgb(8, 2, 2);
    border-radius: 4px;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }
  .button {
    font-size: 1em;
    cursor: pointer;
    color: inherit;
  }
  .button.text {
    font-size: 1em;
  }
  .button.navigation {
    width: 1em;
    height: 1em;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 0.2em;
    border-radius: var(--border-radius);
  }
  .buttons {
    display: flex;
  }
</style>
