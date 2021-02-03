<script>
  import { createEventDispatcher } from 'svelte'

  export let day
  const dispatch = createEventDispatcher()
</script>

<div
  class="day"
  class:disabled="{day.disabled}"
  class:prec="{day.prec}"
  class:next="{day.next}"
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
  .day {
    box-sizing: border-box;
    cursor: pointer;
    border-radius: var(--border-radius);
    display: flex;
    justify-content: center;
    align-items: center;
    border-bottom: 1px solid #d7d7d7;
    border-left: 1px solid #d7d7d7;
  }
  .day:nth-child(7n + 1) {
    border-left: none;
  }
  .day:nth-last-child(-n + 7) {
    border-bottom: none;
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
    border: 1px solid var(--today-background);
  }
  .selected {
    background: var(--selected-background) !important;
  }
  .selected .value {
    font-weight: 700;
    color: var(--selected-color) !important;
  }
  .offmonth > span {
    opacity: 0.2;
  }
  .prec {
    border-left: none;
  }
  .next {
    border-bottom: none;
    border-left: none;
  }
  :global(.day:not(.next) + .next) {
    border-left: 1px solid #d7d7d7;
  }
  .offmonth.selected {
    background: var(--offmonth-background-color);
  }
  .disabled {
    background-color: var(--disabled--background-color) !important;
    pointer-events: none;
  }
  .disabled .value,
  .disabled.weekend .value {
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
