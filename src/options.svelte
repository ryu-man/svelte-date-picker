<script lang="ts">
  import { createEventDispatcher } from 'svelte'
  import { fade } from 'svelte/transition'
  import { today, calendarContext } from './calendar.svelte'
  import { format } from 'date-fns'
  import ArrowLeftIcon from './icons/arrow_left_icon.svelte'
  import ArrowRightIcon from './icons/arrow_right_icon.svelte'

  const dispatch = createEventDispatcher()
  const context = calendarContext()

  let calendar = $context.calendar
  let pivotYear = calendar.getFullYear()

  $: calendar = $context.calendar

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

<div class="options" transition:fade="{{ duration: 200 }}">
  <div class="options-container">
    <div class="months-container">
      {#each getMonths() as month, i}
        <div
          id="{'m' + i}"
          class="month"
          class:today="{month.today}"
          class:selected="{calendar.getMonth() === i}"
          on:click="{() => {
            calendar.setMonth(i)
            $context.calendar = calendar
            dispatch('action')
          }}"
        >
          {month.name}
        </div>
      {/each}
    </div>
    <div class="navigation buttons">
      <div class="prev button" on:click="{() => (pivotYear -= 10)}">
        <ArrowLeftIcon />
      </div>
      <span class="range">{`${pivotYear - 10} - ${pivotYear + 10}`}</span>
      <div class="next button" on:click="{() => (pivotYear += 10)}">
        <ArrowRightIcon />
      </div>
    </div>
    <div class="years-container scrollable">
      <!-- <div class="years" transition:fade> -->
      {#each getYears(pivotYear) as year, i}
        <!-- content here -->
        <div
          id="{'y' + year}"
          class="year"
          class:today="{today.getFullYear() == year}"
          class:selected="{calendar.getFullYear() === year}"
          on:click="{() => {
            calendar.setFullYear(year)
            $context.calendar = calendar
          }}"
        >
          {year}
        </div>
      {/each}
      <!-- </div> -->
    </div>
  </div>
</div>

<style>
  .options {
    display: flex;
    position: fixed;
    overflow: hidden;
    backdrop-filter: blur(8px);
    z-index: 0;
    border-radius: var(--border-radius);
  }
  .options-container {
    position: relative;
    display: flex;
    flex-direction: column;
    width: 100%;
    padding: 0.6em;
  }

  .years-container {
    flex: 1;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    justify-content: center;
    align-items: center;
  }
  .years {
    /* position: absolute; */
    text-align: center;
  }
  .year {
    padding: 0.6em;
    font-size: 1.2em;
    transition: all 0.3s;
  }
  .months-container {
    display: flex;
    justify-content: space-between;
    margin-bottom: 0.7em;
  }
  .months {
    /* position: absolute; */
    text-align: center;
  }
  .month {
    padding: 0.1em;
    font-size: 0.8em;
  }
  .year.selected {
    background-color: var(--weekend-color);
    border-radius: var(--border-radius);
    color: #fff;
    transition: all 0.3s;
  }
  .month.selected {
    color: var(--weekend-color);
    border-bottom: 2px solid var(--weekend-color);
    font-weight: 700;
  }
  .navigation.buttons {
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: var(--weekend-color);
  }
  .navigation.buttons > .button {
    width: 36px;
  }
</style>
