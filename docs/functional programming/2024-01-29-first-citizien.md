---
slug: function-first-citizen
title: FP - 為什麼說函數是一等公民
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [fp, functional programming, javascript]
---

函式是一等公民，這是什麼意思？
就是指 函式跟其他物件一樣 沒有什麼特別。
他可以儲存在陣列裏，當成參數傳遞，賦值給變數，你想怎麼樣都行。
這意味著可以將函數儲存在變數中，將他們傳遞給其他函數。

### 在變數中儲存函數

下面定義 add 函數將函數名稱 `add` 指派給 `sum`

```javascript
function add(a, b) {
  return a + b;
}

let sum = add;
```

我們可以正常的呼叫 `add`

```javascript
let result = add(10, 20);
```

或者可以透過 `sum` 來呼叫

```javascript
let result = sum(10, 20);
```

### 將函數傳給另一個函數

因為函數是值，所以可以將一個函數作為一個參數傳遞給另一個函數
下面聲明 `average()` 接收三個參數的函數。第三個參數是一個函數

```javascript
function average(a, b, fn) {
  return fn(a, b) / 2;
}
```

現在可以將 `sum` 傳遞給 `average()`

```javascript
let result = average(10, 20, sum);
```

把它們放在一起

```javascript
function add(a, b) {
  return a + b;
}

let sum = add;

function average(a, b, fn) {
  return fn(a, b) / 2;
}

let result = average(10, 20, sum);

console.log(result);
// 15
```

### 函數返回函數

由於函數是值，可以從一個函數傳回另一個函數
以下 `compareBy()` 函數回傳一個函數，用於兩個物件屬性比較

```javascript
function compareBy(propertyName) {
  return function (a, b) {
    let x = a[propertyName],
      y = b[propertyName];

    if (x > y) {
      return 1;
    } else if (x < y) {
      return -1;
    } else {
      return 0;
    }
  };
}
```

假如有一個產品物件陣列，其中每個產品物件都有兩個屬性 `name` 和 `price`

```javascript
let products = [
  { name: "iPhone", price: 900 },
  { name: "Samsung Galaxy", price: 850 },
  { name: "Sony Xperia", price: 700 },
];
```

可以透過 `sort()` 對於陣列進行排序，`sort()` 方法接收一個函數作為參數，用於比較陣列的兩個元素

例如：可以基於排序產品名稱 `name` 透過 `compareBy()` 傳遞 `sort()` 函數

```javascript
// Sort by product name

products.sort(compareBy("name"));

console.table(products);

// 輸出如下：

// Products sorted by name:
┌─────────┬──────────────────┬───────┐
│ (index) │       name       │ price │
├─────────┼──────────────────┼───────┤
│    0    │ 'Samsung Galaxy' │  850  │
│    1    │  'Sony Xperia'   │  700  │
│    2    │     'iPhone'     │  900  │
└─────────┴──────────────────┴───────┘
```

同樣，可以按價格對產品物件進行排序：

```javascript
// Sort by product price

products.sort(compareBy('price'));
console.table(products);

// 輸出如下：

// Products sorted by price:
┌─────────┬──────────────────┬───────┐
│ (index) │       name       │ price │
├─────────┼──────────────────┼───────┤
│    0    │  'Sony Xperia'   │  700  │
│    1    │ 'Samsung Galaxy' │  850  │
│    2    │     'iPhone'     │  900  │
└─────────┴──────────────────┴───────┘
```

### JavaScript 函數是一等公民範例

以下範例定義了兩個函數，將以厘米為單位的長度轉換為英寸，反之亦然：

```javascript
function cmToIn(length) {
  return length / 2.54;
}

function inToCm(length) {
  return length * 2.54;
}
```

下面的 `convert()` 函數有兩個參數。第一個參數是一個函數，第二個參數是長度。

```javascript
function convert(fn, length) {
  return fn(length);
}
```

要將 cm 轉換為 in，可以呼叫 `convert()` 函數並將 `cmToIn` 函數作為第一個函數傳遞給 `convert()`

```javascript
let inches = convert(cmToIn, 10);
console.log(inches);

// 3.937007874015748
```

同樣 要把英吋轉為釐米 可以把 `inToCm` 函數傳遞給 `convert()`

```javascript
let cm = convert(inToCm, 10);
console.log(cm);

// 25.4
```

### 結論

- 函數是 javascript 的一等公民，可以稱這種程式為高階函式
- 可以將函數作為參數傳遞給其他函數，將他們作為值從其他函數中返回，並將函數儲存在變數中
