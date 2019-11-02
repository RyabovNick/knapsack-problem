<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <KnapsackProblem />
  </div>
</template>

<script>
// @ is an alias to /src
import KnapsackProblem from '@/components/KnapsackProblem.vue'

export default {
  name: 'home',
  components: {
    KnapsackProblem
  }
}
</script>
