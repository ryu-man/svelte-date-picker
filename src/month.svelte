<script>
  import { format } from 'date-fns'
  import { createEventDispatcher } from 'svelte'
  import { today } from './calendar.svelte'
  import { getCalendarContext } from './context.js'
  import Day from './day.svelte'

  const dispatch = createEventDispatcher()

  export let disablePredicate
  let x = 72

  const { selectedContext, calendarContext } = getCalendarContext()

  $: days = generator($calendarContext, $selectedContext)

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
    let next=false, prec=false
    for (let index = 0; index < 42; index++) {
      sample.setDate(sample.getDate() + 1)
      prec = calendar.getMonth() > sample.getMonth() || calendar.getFullYear() > sample.getFullYear()
      next = (calendar.getMonth() < sample.getMonth() && (calendar.getFullYear() === sample.getFullYear())) || 
             (calendar.getMonth() > sample.getMonth() && (calendar.getFullYear() < sample.getFullYear()))

      array.push({
        id: sample.getTime(),
        date: sample.getDate(),
        offmonth: next||prec,
        next,
        prec,
        today: today.getTime() == sample.getTime(),
        week: Math.floor(index / 7),
        month: sample.getMonth(),
        disabled: disablePredicate(sample),
        weekend: sample.getDay() == 0,
        name: format(sample, 'iiiii'),
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
        if (day.disabled) return
        if ($selectedContext) {
          $selectedContext.setTime(+e.detail)
        } else {
          $selectedContext = new Date(+e.detail)
        }
        $selectedContext = $selectedContext
        dispatch('action')
      }}"
    />
  {/each}
</div>

<style>
  .month {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    /* grid-row-gap: 0.4em; */
    /* grid-column-gap: 0.4em; */
    width: 100%;
    height: 100%;
  }
  .label {
    font-weight: 500;
    border-bottom: 1px solid #d7d7d7
  }
  .label.weekend {
    color: var(--weekend-color);
  }
</style>
