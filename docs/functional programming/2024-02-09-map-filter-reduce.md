---
slug: fp-map-filter-reduce
title: FP - 常用的工具與技巧 Map、Filter、Reduce
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=38043a6390fdf82e3a2058d5a76e44345f8f6327&v=4
tags: [fp, functional programming, javascript]
---

### Map

在 Javascript 中，我們有[Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)可以幫助我們將 Array 中 element 依照 callback function 規則一一對應到新的 array，這有助於我們輕鬆的將元素統一『擴展』、『轉換』。

簡單的範例：將 array 每一個元素都乘 5

```javascript
const multi5 = (x) => x * 5;
let arr = [1, 2, 3, 4, 5];
const result = arr.map(multi5);
console.log(result);
// [5,10,15,20,25]
```

### Filter

[Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)是 Javascript 中原生支援的方法，與`Map`不同的地方是他會依照 `callback function` 中的規則篩選條件符合的 element 並且搜集成新的 array

簡單的範例：僅留下陣列中的偶數

```javascript
const isEven = (x) => x % 2 === 0;
let arr = [1, 2, 3, 4, 5];
const result = arr.filter(isEven);
console.log(result);
// [2,4]
```

### Reduce

[Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)一樣是 Javascript 中原生支援的方法，這個方法與前兩者有著極大的區別。

#### reduce 的基礎語法

```javascript
Array.reduce(
  callback[(accumulator, currentValue, currentIndex, array)],
  initialValue
);
```

- accumulator：經由個別 `currentValue` 加總的累計值
- currentValue：`Array` 的個別 item
- currentIndex：`Array` item 的索引
- array：呼叫該 `Array method` 的陣列
- initialValue：預設值，放在 `function` 的最後方，非必填

其中比較特別的是 `accumulator` 與 `initialValue`，在其他的陣列方法裡也少見。用文字敘述可能會不太清楚這些參數的意思，這裡用最簡單的陣列數值加總為例，並預設兩種不同狀況：

簡單的範例：

#### 1. 計算最大值
```javascript
const arr = [1, 2, 3, 4];
const initVal = 0;
const result = arr.reduce(
  (accumulator, current) => accumulator + current,
  initVal
);
console.log(result);
// 10
```

| loop 第幾次 | accumulator | current value | return value |
| ----------- | ----------- | ------------- | ------------ |
| 1           | 1           | 2             | 3            |
| 2           | 3           | 3             | 6            |
| 3           | 6           | 4             | 10           |

#### 2. 將陣列中重複的元素去除

```javascript
const numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4];
const uniqueNumbers = numbers.reduce((acc, current) => {
  if (!acc.includes(current)) {
    acc.push(current);
  }
  return acc;
}, []);
console.log(uniqueNumbers); // [1, 2, 3, 4]
```

#### 3. 計算每個元素出現的次數

```javascript
const numbers = [1, 2, 3, 2, 2, 3, 3, 4, 2, 4];
const count = numbers.reduce((acc, current) => {
  if (acc[current]) {
    acc[current] += 1;
  } else {
    acc[current] = 1;
  }
  return acc;
}, {});
console.log(count); // {1: 1, 2: 4, 3: 3, 4: 2}
```

#### 4. 將二維陣列攤平

```javascript
const arrays = [[1, 2], [3, 4], [5, 6]];
const flattened = arrays.reduce((acc, current) => acc.concat(current), []);
console.log(flattened); // [1, 2, 3, 4, 5, 6]
```

#### 5. 根據物件中的某個屬性進行分組

```javascript
const data = [
  { id: 1, category: 'A' },
  { id: 2, category: 'B' },
  { id: 3, category: 'A' },
  { id: 4, category: 'C' },
  { id: 5, category: 'B' }
];

const groupedData = data.reduce((acc, current) => {
  (acc[current.category] = acc[current.category] || []).push(current);
  return acc;
}, {});

console.log(groupedData);
// {
//   A: [{ id: 1, category: 'A' }, { id: 3, category: 'A' }],
//   B: [{ id: 2, category: 'B' }, { id: 5, category: 'B' }],
//   C: [{ id: 4, category: 'C' }]
// }
```
