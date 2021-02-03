<script lang="ts">
  import { createEventDispatcher } from 'svelte'
  import { fade } from 'svelte/transition'
  import { today } from './calendar.svelte'
  import { format } from 'date-fns'
  import { getCalendarContext } from './context.js'
  import ArrowLeftIcon from './icons/arrow_left_icon.svelte'
  import ArrowRightIcon from './icons/arrow_right_icon.svelte'

  const dispatch = createEventDispatcher()
  const { selectedContext, calendarContext } = getCalendarContext()

  let pivotYear = $calendarContext.getFullYear()

  function getMonths() {
    const sample = new Date()
    const array = []
    for (let index = 0; index < 12; index++) {
      sample.setMonth(index)
      array.push({
        index,
        name: format(sample, 'MMM'),
        today:
          index === today.getMonth() &&
          sample.getFullYear() === today.getFullYear()
      })
    }
    return array
  }

  function getYears(pivot) {
    const array = []
    for (let index = pivot - 10 + 1; index <= pivot + 10; index++) {
      array.push(index)
    }
    return array
  }
</script>

<div class="options" transition:fade="{{ duration: 200, delay:100 }}">
  <div class="inner-options">
    <div class="months-container">
      {#each getMonths() as month, i}
        <div
          id="{'m' + i}"
          class="month"
          class:today="{month.today}"
          class:selected="{$calendarContext.getMonth() === i}"
          on:click="{() => {
            $calendarContext.setMonth(i)
            $calendarContext = $calendarContext
            dispatch('action')
          }}"
        >
          {month.name}
        </div>
      {/each}
    </div>
    <div class="years-container">
      <nav>
        <div class="prev button" on:click="{() => (pivotYear -= 10)}">
          <ArrowLeftIcon />
        </div>
        <span class="range">{`${pivotYear - 10} - ${pivotYear + 10}`}</span>
        <div class="next button" on:click="{() => (pivotYear += 10)}">
          <ArrowRightIcon />
        </div>
      </nav>
      <div class="years-grid scrollable">
        <!-- <div class="years" transition:fade> -->
        {#each getYears(pivotYear) as year, i}
          <!-- content here -->
          <div
            id="{'y' + year}"
            class="year"
            class:today="{today.getFullYear() == year}"
            class:selected="{$calendarContext.getFullYear() === year}"
            on:click="{() => {
              $calendarContext.setFullYear(year)
              $calendarContext = $calendarContext
            }}"
          >
            {year}
          </div>
        {/each}
        <!-- </div> -->
      </div>
    </div>
  </div>
</div>

<style>
  .options {
    display: flex;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    overflow: hidden;
    background-color: #fff;
    border-radius: var(--border-radius);
    z-index: 0;

    --border-width: 1px;
    --border-color: #d7d7dd
  }
  .inner-options {
    position: relative;
    display: flex;
    width: 100%;
  }
  .years-container{
    display: flex;
    flex-direction: column;
    flex: 1;
    color: rgb(58, 58, 58);
  }
  .years-grid {
    flex: 1;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    justify-content: center;
    align-items: center;
  }
  .year {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: all 0.3s;
    border-bottom: var(--border-width) solid var(--border-color);
    border-right: var(--border-width) solid var(--border-color);
  }
  .year:nth-child(4n){
    border-right: none;

  }
  .year:nth-child(n+17){
    border-bottom: none;

  }
  .year.selected {
    background-color: var(--weekend-color);
    border-radius: var(--border-radius);
    color: #fff;
    transition: all 0.3s;
  }
  .months-container {
    padding-right: .2em;
    border-right: var(--border-width) solid var(--border-color);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }
  .month {
    padding: 0.1em;
    font-size: 0.8em;
    flex:1;
  }
  .month.selected {
    color: var(--weekend-color);
    border-left: 2px solid var(--weekend-color);
    font-weight: 700;
  }
  nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: var(--border-width) solid var(--border-color);
  }
  nav > .button {
    width: 36px;
  }
</style>
