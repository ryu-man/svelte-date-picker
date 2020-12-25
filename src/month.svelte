<script>
  import { format } from 'date-fns'
  import { createEventDispatcher } from 'svelte'
  import Day from './day.svelte'
  import { calendarContext, today } from './calendar.svelte'
  import Transition from './transition.svelte'
  import { fade, fly, scale } from 'svelte/transition'
  import { linear } from 'svelte/easing'

  const dispatch = createEventDispatcher()

  export let disablePredicate
  let x = 72

  const context = calendarContext()
  let calendar = $context.calendar

  $: calendar = $context.calendar
  $: selected = $context.selected
  $: days = generator(calendar, selected)

  const generator = function (calendar, selected) {
    const firstDay = new Date(
      calendar.getFullYear(),
      calendar.getMonth(),
      1
    ).getDay()
    const lastMonthDaysCount = monthDays(
      calendar.getMonth() - 1,
      calendar.getFullYear()
    )
    const sample = new Date(
      calendar.getFullYear(),
      calendar.getMonth() - 1,
      lastMonthDaysCount - firstDay
    )
    const array = []
    for (let index = 0; index < 42; index++) {
      sample.setDate(sample.getDate() + 1)
      array.push({
        id: sample.getTime(),
        date: sample.getDate(),
        offmonth: calendar.getMonth() != sample.getMonth(),
        today: today.getTime() == sample.getTime(),
        week: Math.floor(index / 7),
        month: sample.getMonth(),
        disabled: disablePredicate(sample),
        weekend: sample.getDay() == 0,
        name: format(sample, 'iii'),
        selected: selected?.getTime() === sample.getTime()
      })
    }
    return array
  }

  function monthDays(month, year = 2020) {
    return new Date(year, month + 1, 0).getDate()
  }

  function scle(node, { delay = 0, duration = 400, easing = backInOut }) {
    return {
      delay,
      duration,
      easing,
      css: (t, u) => {
        return `transform: scale(${u})`
      }
    }
  }
</script>

<div class="month">
  {#each days.filter((d) => d.week == 1) as day}
    <div class="label" class:weekend="{day.weekend}">{day.name}</div>
  {/each}
  {#each days as day (day.id)}
    <Day
      day="{day}"
      on:action="{function (e) {
        if(day.disabled) return
        if (selected) {
          selected.setTime(+e.detail)
        } else {
          selected = new Date(+e.detail)
        }
        $context.selected = selected
        dispatch('action')
      }}"
    />
  {/each}
</div>

<style>
  .month {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-row-gap: 0.4em;
    grid-column-gap: 0.4em;
    width: 100%;
    height: 100%;
  }
  .label {
    font-weight: 500;
  }
  .label.weekend {
    color: var(--weekend-color);
  }
</style>
