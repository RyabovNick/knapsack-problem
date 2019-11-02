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
            class="display-2 font-weight-bold mb-1 pa-1"
          >Welcome to Knapsack-Problem</h1>
        </v-row>
      </v-col>
      <v-col cols="12">
        <v-row justify="center">
          <p class="subheading font-weight-regular pa-1">
            Дано:
            <i>n</i> предметов с заданным весом и ценностью, размер рюкзака.
            <br />Надо найти наиболее ценный набор предметов, который помещается в рюкзак.
          </p>
        </v-row>
      </v-col>

      <v-col cols="12">{{ executionTime }}</v-col>

      <v-col cols="10" offset="1" sm="4" offset-sm="4">
        <v-row justify="center">
          <v-text-field
            type="number"
            v-model.number="bagSize"
            label="Введите размер рюкзака"
            class="max-width"
          ></v-text-field>
        </v-row>
      </v-col>

      <v-col
        cols="10"
        offset="1"
        sm="6"
        offset-sm="3"
        md="4"
        offset-md="1"
        lg="3"
        offset-lg="3"
      >
        <v-text-field
          class="pa-1"
          v-model="newItem.name"
          label="Название"
          @input="addItemButtonState()"
        ></v-text-field>
      </v-col>
      <v-col cols="4" md="2" lg="1">
        <v-row justify="center">
          <v-text-field
            class="pa-1"
            v-model="newItem.value"
            type="number"
            label="Ценность"
          ></v-text-field>
        </v-row>
      </v-col>
      <v-col cols="4" md="2" lg="1">
        <v-row justify="center">
          <v-text-field
            class="pa-1"
            v-model="newItem.size"
            type="number"
            label="Вес"
          ></v-text-field>
        </v-row>
      </v-col>
      <v-col cols="4" md="2" lg="1">
        <v-row justify="center">
          <v-btn
            class="mx-2"
            fab
            dark
            color="indigo"
            @click="addItem()"
            :disabled="disabled"
          >
            <v-icon dark>mdi-plus</v-icon>
          </v-btn>
        </v-row>
      </v-col>

      <v-col cols="3" offset="3">
        <v-row justify="center">
          <v-text-field
            class="pa-1"
            v-model="itemsCount"
            type="number"
            label="Кол-во предметов"
          ></v-text-field>
        </v-row>
      </v-col>

      <v-col cols="3">
        <v-row justify="center">
          <v-btn @click="autoGenerate()" color="success">Сгенерировать предметы</v-btn>
        </v-row>
      </v-col>

      <v-col cols="12">
        <v-row justify="center">
          <v-btn @click="knapsackDynamic()" color="success">Найти предметы</v-btn>
        </v-row>
      </v-col>

      <v-col cols="12">
        <v-row justify="center">
          <v-data-table :headers="headers" :items="items" class="elevation-1">
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
    ],
    bagSize: 9,
    itemsCount: 8,
    newItem: {
      name: '',
      value: 1,
      size: 1,
      select: false
    },
    disabled: true,
    executionTime: [] as any[]
  }),
  methods: {
    knapsackDynamic(): boolean {
      const start: number = new Date().getTime()
      const values: number[] = [0, ...this.items.map((item) => item.value)]
      const weights: number[] = [0, ...this.items.map((item) => item.size)]
      const N: number = this.items.length

      let M: any[] = this.arrayGen(this.bagSize, N)
      let keepArr: any[] = this.arrayGen(this.bagSize, N)

      for (let i = 1; i <= N; i++) {
        for (let j = 0; j <= this.bagSize; j++) {
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

      let keptElements: number[] = []
      let eleKey: number = this.bagSize
      for (let i = N; i > 0; i--) {
        if (keepArr[i][eleKey] == 1) {
          keptElements.push(i - 1)
          eleKey -= weights[i]
        }
      }

      this.executionTime.push(new Date().getTime() - start)

      for (const el of this.items) {
        el.select = false
      }

      for (const el of keptElements) {
        this.items[el].select = true
      }

      return true
    },
    arrayGen: (bagSize: number, N: number) => {
      let M = new Array(bagSize + 1)
      M[0] = new Array(bagSize + 1).fill(0)
      for (let i = 1; i <= N; i++) {
        M[i] = new Array(bagSize)
      }
      return M
    },
    autoGenerate(count: number): boolean {
      const newItemArray = []

      for (let i = 0; i < this.itemsCount; i++) {
        newItemArray.push({
          name: faker.commerce.product(),
          value: this.getRandomInt(1, 50),
          size: this.getRandomInt(1, 15),
          select: false
        })
      }

      this.items = newItemArray

      return true
    },
    getRandomInt(min: number, max: number): number {
      min = Math.ceil(min)
      max = Math.floor(max)
      return Math.floor(Math.random() * (max - min)) + min //The maximum is exclusive and the minimum is inclusive
    },
    addItem(): boolean {
      this.items.push({ ...this.newItem })
      return true
    },
    addItemButtonState(): boolean {
      if (this.newItem.name.length === 0) {
        this.disabled = true
        return true
      }

      this.disabled = false
      return true
    }
  }
})
</script>

<style scoped>
.max-width {
  max-width: 220px;
}
</style>