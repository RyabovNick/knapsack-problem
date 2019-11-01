<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12">
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        ></v-img>
      </v-col>

      <v-col cols="12">
        <v-row justify="center">
          <h1
            class="display-2 font-weight-bold mb-3"
          >Welcome to Knapsack-Problem</h1>
          <p class="subheading font-weight-regular">
            Дано:
            <i>n</i> предметов с заданным весом и ценностью, размер рюкзака.
            <br />Надо найти наиболее ценный набор предметов, который помещается в рюкзак.
          </p>
        </v-row>
      </v-col>

      <v-col cols="12">
        <v-data-table
          :headers="headers"
          :items="items"
          :items-per-page="-1"
          class="elevation-1"
          hide-default-footer
        >
          <template v-slot:item="{ item }">
            <tr v-bind:class="[item.select ? 'green' : '']">
              <td>
                <v-text-field v-model="item.name"></v-text-field>
              </td>
              <td>
                <v-text-field v-model="item.value" type="number"></v-text-field>
              </td>
              <td>
                <v-text-field v-model="item.size" type="number"></v-text-field>
              </td>
            </tr>
          </template>
        </v-data-table>
      </v-col>

      <v-col cols="12">
        <v-row justify="center">
          <v-btn @click="knapsack()">test</v-btn>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
const faker = require('faker')

export default Vue.extend({
  data: () => ({
    headers: [
      { text: 'Название', value: 'name' },
      { text: 'Ценность', value: 'value' },
      { text: 'Вес', value: 'size' }
    ],
    items: [
      { name: 'Молоко', value: 4, size: 1, select: false },
      { name: 'Ноутбук', value: 40, size: 8, select: false },
      { name: 'Пряники', value: 30, size: 8, select: false },
      { name: 'Крекер', value: 6, size: 2, select: false },
      { name: 'Брюки', value: 10, size: 1, select: false },
      { name: 'Футболка', value: 7, size: 5, select: false },
      { name: 'Бриллиант', value: 10, size: 2, select: false }
    ]
  }),
  methods: {
    knapsack(): string {
      // faker.locale = 'ru'
      for (let i = 0; i < 100; i++) {
        i++
      }

      const bagSize: number = 8

      const values = [0, ...this.items.map((item) => item.value)]
      const weights = [0, ...this.items.map((item) => item.size)]
      const N = this.items.length

      const arrayGen = (m: number, n: number) => {
        let M = new Array(bagSize + 1)
        M[0] = new Array(bagSize + 1).fill(0)
        for (let i = 1; i <= N; i++) {
          M[i] = new Array(bagSize)
        }
        return M
      }

      let M = arrayGen(bagSize, N)
      let keepArr = arrayGen(bagSize, N)

      for (let i = 1; i <= N; i++) {
        for (let j = 0; j <= bagSize; j++) {
          if (weights[i] > j) {
            // we can't add
            M[i][j] = M[i - 1][j]
            keepArr[i][j] = 0 // we can't keep this item
          } else {
            const valueIfLeft = M[i - 1][j]
            const valueIfTaken = M[i - 1][j - weights[i]] + values[i]
            if (valueIfTaken >= valueIfLeft) {
              // maximize the value
              M[i][j] = valueIfTaken
              keepArr[i][j] = 1 // we can keep this item
            } else {
              M[i][j] = valueIfLeft
              keepArr[i][j] = 0
            }
          }
        }
      }

      let keptElements = []
      let eleKey = bagSize
      for (let i = N; i > 0; i--) {
        if (keepArr[i][eleKey] == 1) {
          keptElements.push(i - 1)
          eleKey -= weights[i]
        }
      }

      for (const el of keptElements) {
        this.items[el].select = true
      }

      return 'test'
    }
  }
})
</script>
