<script>
  let intro = [() => {}, {}]
  export {intro as in}
  let outro = intro
  export {outro as out}
  let introFunc = intro[0]
  let introParams = intro[1]
  let outroFunc = outro[0]
  let outroParams = outro[1]
</script>

<div
  class="transition"
  in:introFunc="{introParams}"
  out:outroFunc="{outroParams}"
  on:introstart
  on:introend
  on:outrostart
  on:outroend
>
  <slot>
    <!-- optional fallback -->
  </slot>
</div>

<style>
  .transition{
    height: 100%;
    width: 100%;
  }
</style>
