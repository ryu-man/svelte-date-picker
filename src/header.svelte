<script>
  import { format } from 'date-fns'
  import { calendarContext } from './calendar.svelte'
  import ArrowLeftIcon from './icons/arrow_left_icon.svelte'
  import ArrowRightIcon from './icons/arrow_right_icon.svelte'

  export let index = 0
  export let locale = 'en-US'

  const context = calendarContext()
  $: calendar = $context.calendar

  function next() {
    calendar.setMonth(calendar.getMonth() + 1)
    $context.calendar = calendar
  }

  function prev() {
    calendar.setMonth(calendar.getMonth() - 1)
    $context.calendar = calendar
  }
</script>

<div class="header">
  <div
    class="text button"
    on:click="{function () {
      index = 1
    }}"
  >
    {format(calendar, 'MMMM yyyy')}
  </div>
  <div class="buttons">
    <div
      class="navigation button "
      role="button"
      on:click="{prev.call(calendar)}"
    >
      <ArrowLeftIcon />
    </div>
    <div
      class="navigation button"
      role="button"
      on:click="{next.call(calendar)}"
    >
      <ArrowRightIcon />
    </div>
  </div>
</div>

<style>
  .header {
    padding: 0.3em 0;
    margin-bottom: 1vh;
    font-size: 1.2em !important;
    font-weight: 700;
    color: rgb(90, 90, 90);
    border-radius: 4px;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: baseline;
  }
  .button {
    font-size: 1em;
    cursor: pointer;
    color: inherit;
    padding: 0.3em;
  }
  .button.text {
    font-size: 1.2em;
  }
  .button.navigation {
    width: 1em;
    height: 1em;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: var(--weekend-color);
    color: var(--background);
    margin: 0 0.2em;
    border-radius: var(--border-radius);
  }
  .buttons {
    display: flex;
  }
  
</style>
