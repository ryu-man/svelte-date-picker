<script>
  import { createEventDispatcher } from 'svelte'

  export let day
  const dispatch = createEventDispatcher()
</script>

<div
  class="day"
  class:disabled="{day.disabled}"
  class:offmonth="{day.offmonth}"
  class:weekend="{day.weekend}"
  class:today="{day.today}"
  class:selected="{day.selected}"
  on:click="{function () {
    dispatch('action', day.id)
  }}"
  style="font-size:.9em"
>
  <span class="value">{day.date}</span>
</div>

<style>
  :root {
    --day-width: 1.5em;
  }
  .day {
    box-sizing: border-box;
    cursor: pointer;
    border-radius: var(--border-radius);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .day:hover {
    background-color: rgba(0, 0, 0, 0.072);
  }
  .value {
    color: rgb(36, 36, 36);
    width: 34px;
    height: 34px;
    line-height: 34px;
  }
  .today:not(.selected) {
    box-shadow: 0 0 0vw 0.1em var(--today-background);
  }
  .selected {
    background: var(--selected-background) !important;
  }
  .selected .value {
    font-weight: 700;
    color: var(--selected-color) !important;
  }
  .offmonth .value {
    color: var(--offmonth-color);
  }
  .offmonth.selected {
    background: var(--offmonth-background-color);
  }
  .disabled {
    background-color: var(--disabled--background-color) !important;
    pointer-events: none;
  }
  .disabled .value, .disabled.weekend .value{
    color: var(--disabled-color) !important;
    pointer-events: none;
  }
  .weekend .value {
    color: var(--weekend-color) !important;
  }
  .weekend.selected .value {
    color: var(--selected-color) !important;
  }
  .weekend.offmonth .value,
  .weekend.disabled .value {
    color: var(--weekend-color);
  }
</style>
