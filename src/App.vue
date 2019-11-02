<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>KNAPSACK</span>
        <span class="font-weight-light">PROBLEM</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        text
        href="https://github.com/RyabovNick/knapsack-problem"
        target="_blank"
      >
        <span class="mr-2">Github</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <KnapsackProblem />
    </v-content>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue'
import KnapsackProblem from './components/KnapsackProblem.vue'

export default Vue.extend({
  name: 'App',
  components: {
    KnapsackProblem
  },
  data: () => ({
    //
  })
})
</script>
