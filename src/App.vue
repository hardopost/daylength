<template>
  <div id="app">
    <DayLengthEnter/>
  </div>
</template>



<script>
import DayLengthEnter from './components/DayLengthEnter.vue'
export default {
  name: 'App',
  components: {
      DayLengthEnter,
  }
}
</script>



<style>

#app {
    margin-top: 60px;
}

</style>
