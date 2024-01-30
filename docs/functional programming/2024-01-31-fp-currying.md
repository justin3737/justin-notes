---
slug: fp-currying
title: FP - Currying 函數柯里化
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [fp, functional programming]
---

介紹 Functional Programming 重要的概念，Curry。

### Curry 的功用？

`柯里化（Currying）`是將接受多個參數的函式轉變為一系列接受單個參數的函式的過程。柯里化的目的是將函式變得更具彈性，可以部分應用（partial application）參數，延遲參數的傳遞或創建可組合的函式。

我們先來看一個簡單的函式 `add`

```javascript
const add = (x, y) => x + y;
```

正常使用方法：

```javascript
add(1, 1); // 2
```

那如果多放或少放參數呢?

```javascript
add(1); // NaN

add(1, 2, "Naurrr"); //2
```

多放參數也不是不行，只是會被忽視。 少放的話則會發生問題，變成 `1+undefined`，則為 `NaN`

> 相較於一般函式寫法不同，Currying 這個概念在於將原本預期是要傳入多個參數的函式 轉化成 每次只接收一個參數作為輸入，並回傳新的函式，且等待下一個參數傳入，直到函式所需參數皆到齊時，才會呼叫。

所以在 Currying 下的 add 函式寫法就會是

```javascript
const addC = (x) => (y) => x + y;
```

這時 `addC` 需要這樣呼叫 `addC(1)(1)` ，好處就是我們可以不用一次放入全部參數，可以最大化函式的自由度，也就是說可以在任何時間與任何地點(不同檔案路徑) 呼叫 `currying` 的函式。

```javascript
// utils.js
export const add1 = addC(1);

// example.js
import { add1 } from "utils.js";

[1, 2, 3, 4, 5].map(add1);
// [2,3,4,5,6]
```

### 實作 Curry

既然知道 `currying` 這個概念了，直接來實作一個可以將各種函式 `currying` 的通用函式。

```javascript
function curry(fn, arity = fn.length) {
  return (function nextCurried(prevArgs) {
    return function curried(nextArgs) {
      const args = [...prevArgs, nextArgs];
      if (args.length >= arity) {
        return fn(...args);
      }
      return nextCurried(args);
    };
  })([]);
}
```

現在就來將原本的 `add` 透過 `curry` 變成有 `currying` 特性的 `add` 吧 !

```javascript
const add = (x, y) => x + y;

const addC = curry(add);

addC(1)(1);
// 2
```

分析一下 curry 這個函式是怎麼將各種函數通用化的，舉 `add(x, y)` 來說，

一開始 `curry(add)` 傳入時會馬上呼叫 nextCurried 並且 `prevArgs` 的初始值為 `[]`，

當 1 被傳入時，此時 `args` 會變成 `[1]`，此時 `args.length` 還小於 `add` 傳入參數長度`(2)`， 所以會進行遞迴 `nextCurried([1])` 。

當另一個 1 被傳入時，此時 `args` 會變成 `[1, 1]`，而 `args.length` 也是 `2`， 故收到所有參數的 add 會立即執行，並回傳 `2`。

### 來個 Curry 範例

想像一下，現再有一隻 [Users API](https://jsonplaceholder.typicode.com/users)，需求是

> 1. 對使用者居住的緯度由高到低進行排序，
> 2. 取出使用者的名字，
> 3. 在每位使用者名子後面加 "!"。

所需要的函式會有:

```javascript
// sort
const sort = (fn, data) => [...data].sort(fn);

// get
const get = (key, data) => data[key];

// concat
const concat = (symbol, data) => data.concat(symbol);

// map
const map = (transformer, data) => data.map(transformer);
```

上面可以看到一個共通點： `datalist` 在後面，這也是 `ramda` 或 `lodash/fp` 的特色！將需要處理的資料放在後面。

由於函式沒有進行 currying 之前，函式需要一次傳入其所需之參數

```javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then((data) => sort((a, b) => b.address.geo.lat - a.address.geo.lat, data))
  .then((sortedData) => map((data) => get("username", data), sortedData))
  .then((usernames) => map((username) => concat("!", username), usernames))
  .then((result) => console.log(result))
  .catch((err) => console.error(err));
```

接下來，可以試著把所有函式進行 currying，並且重構上面的那段程式碼

```javascript
// sort()
const sort = curry((fn, data) => [...data].sort(fn));

// get()
const get = curry((key, data) => data[key]);

// concat()
const concat = curry((symbol, data) => data.contact(symbol));

// map()
const map = curry((transformer, data) => data.map(transformer));
```

```javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then((xs) => sort((a, b) => b.address.geo.lat - a.address.geo.lat)(xs))
  .then((xs) => map(get("username"))(xs));
  .then((xs)=>map(concat("!")))
  .then(result => console.log(result))
  .catch(err => console.error(err));
```

接著可以把上面的程式碼再簡化：
這就是 `point-free-style` 的威力！

```javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then(sort((a, b) => b.address.geo.lat - a.address.geo.lat))
  .then(map(get("username")))
  .then(map(concat("!")))
  .then(console.log)
  .catch(console.error);
```

#### Reference:

- 關於`point free` 可以參考[Pointfree 编程风格指南](https://www.ruanyifeng.com/blog/2017/03/pointfree.html)
