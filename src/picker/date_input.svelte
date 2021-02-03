<script>
  import { format as dateFormat } from 'date-fns'
  export let format
  export let date

  function formatter(date, format) {
    try {
      return dateFormat(date, format)
    } catch (error) {
      return format
    }
  }
</script>

<span class="date-input"> {formatter(date, format)} </span>

<style>
  .date-input {
    background-color: rgb(0 0 0 / 3%);
    border-radius: 1px;
    padding: 3px;
  }
</style>
