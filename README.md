# knapsack-problem

```typescript
function knapsackDynamic(): boolean {
  const start: number = new Date().getTime()
  const values: number[] = [0, ...this.items.map((item) => item.value)]
  const weights: number[] = [0, ...this.items.map((item) => item.size)]
  const N: number = this.items.length

  let M: any[] = this.arrayGen(this.bagSize, N)
  let keepArr: any[] = this.arrayGen(this.bagSize, N)

  for (let i = 1; i <= N; i++) {
    for (let j = 0; j <= this.bagSize; j++) {
      if (weights[i] > j) {
        // не можем добавить, т.к. вес больше
        M[i][j] = M[i - 1][j]
        keepArr[i][j] = 0 // не можем сохранить этот предмет
      } else {
        const valueIfLeft = M[i - 1][j]
        const valueIfTaken = M[i - 1][j - weights[i]] + values[i]
        if (valueIfTaken >= valueIfLeft) {
          // максимизируем значение
          M[i][j] = valueIfTaken
          keepArr[i][j] = 1 // берём данные предмет
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

  this.selectedItems = []
  this.selectedValue = 0
  this.selectedSize = 0

  for (const el of keptElements) {
    this.items[el].select = true
    this.selectedValue += this.items[el].value
    this.selectedSize += this.items[el].size
    this.selectedItems.push({
      index: el,
      name: this.items[el].name,
      value: this.items[el].value,
      size: this.items[el].size
    })
  }

  return true
}

function arrayGen (bagSize: number, N: number) => {
  let M = new Array(bagSize + 1)
  M[0] = new Array(bagSize + 1).fill(0)
  for (let i = 1; i <= N; i++) {
    M[i] = new Array(bagSize)
  }
  return M
}
```

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Run your tests

```
npm run test
```

### Lints and fixes files

```
npm run lint
```

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).
