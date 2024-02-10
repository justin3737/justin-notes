---
slug: fp-good-part
title: FP 的優點
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [fp, functional programming, javascript]
---

### 可讀性、可預期、可重用

### 可讀性(Readability)

FP 相較於 imperative programming (命令式程式設計) 有著更佳的可讀性，意味著：不僅可以減少犯錯機會，也讓讀程式碼的人，更舒服的理解該程式碼在處理什麼問題。

範例 1: 以下有一個例子：以下有一個陣列我們將他取出`偶數`再`乘二`。

```javascript
// 取偶數
const isEven = (val) => val % 2 === 0;
// 乘以二
const double = (val) => val * 2;

// data
const arr = [1, 2, 3, 4, 5];

// imperative 寫法：
let result = [];
for (let i = 0; i < arr.length; i++) {
  if (isEven(i)) {
    result.push(double(i));
  }
}
console.log(result);
// [4,8];

// FP 寫法：
const result = arr.filter(isEven).map(double);

console.log(result);
// [4,8];
```

範例 2: 利用 curry、 compose 來改寫:

```javascript
// 取偶數
const isEven = (val) => val % 2 === 0;
// 乘以二
const double = (val) => val * 2;

// curry function
const curry =
  (fn) =>
  (...args) =>
    args.length >= fn.length ? fn(...args) : curry(fn.bind(null, ...args));

// compose function
const compose =
  (...fns) =>
  (initialValue) =>
    fns.reduceRight((acc, fn) => fn(acc), initialValue);

// data
const arr = [1, 2, 3, 4, 5];

// 使用curry 來組裝  double
const doubleC = curry((arr) => arr.map(double));

// 使用curry 來組裝 isEven
const isEvenC = curry((arr) => arr.filter(isEven));

// 使用組合技 compose
//  一行的可讀性是否又更高了呢 ?
const filterAanDouble = compose(doubleC, isEvenC);

console.log(filterAanDouble(arr));
// [4,8];
```

詳情可以見[curry()](https://justin-notes.vercel.app/docs/functional%20programming/fp-currying)、 [compose()](https://justin-notes.vercel.app/docs/functional%20programming/fp-composition) 用法。

### 可預期(Predictability)

當我們在寫程式的同時，最害怕就是不可預期的錯誤，而 FP 本身就是 immutable 以及 pure function 的特性使我們減少程式內不可預期的結果，也減少 Side Effect 的產生

### 可重用(Reusable)

每個函式都只處理一件事是 FP 的特色，就好比樂高(Lego)積木，每塊積木只有一個功能，但組合再一起就會變成一個很驚人的模型，這些積木可以重複使用，組合出各式各樣的功能。
