---
slug: fp-composition
title: FP - Function Composition 函數的組合
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [fp, functional programming]
---

函數可以任意組合得到新的 Function：把多個功能單一的函式組合成一個複雜的程式。

### 組合 (compose)

`f` 和 `g` 都是函數，`x` 是他們之間通過管道的值。

```javascript
var compose = function (f, g) {
  return function (x) {
    return f(g(x));
  };
};
```

組合用法如下：

```javascript
// 轉大寫
const upperCase = (x) => x.toUpperCase();
// 驚呼
const exclaim = (x) => x + "!";
const shout = compose(exclaim, upperCase);
shout("send in the clowns");
//=> "SEND IN THE CLOWNS!"
```

在 compose 的定義中 `g` 先行於 `f`，是由右向左的資料流（左傾）。

我們來看一個順序很重要的例子：

```javascript
// 取第一個
var head =  (x) => x[0];
// 反轉
const reverse = reduce((acc, x) =>
   [x].concat(acc)
}, []);
const last = compose(head, reverse);

last(["jumpkick", "roundhouse", "uppercut"]);
//=> 'uppercut'
```

### 结合律（associativity）

```javascript
const associative = compose(f, compose(g, h)) == compose(compose(f, g), h);
// true
```

這個特性就是結合律，符合結合律意味著不管你是把`g`和`h`分到一組，還是把`f`和`g`分到一組都不重要。所以，如果我們想把字串變成大寫，可以這麼寫：

```javascript
compose(toUpperCase, compose(head, reverse));

// 或者
compose(compose(toUpperCase, head), reverse);
```

因為如何為 的 `compose` 呼叫分組不重要，所以結果都是一樣的。這也讓我們有能力寫出一個可變的組合（variadic compose），用法如下：

```javascript
const lastUpper = compose(toUpperCase, head, reverse);

lastUpper(["jumpkick", "roundhouse", "uppercut"]);
//=> 'UPPERCUT'

const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

loudLastUpper(["jumpkick", "roundhouse", "uppercut"]);
//=> 'UPPERCUT!'
```

### 與上一篇 Curry 範例做結合

看一下先前的程式碼：

```javascript
// util.js

// sort
const sort = curry((fn, data) => [...data].sort(fn));
// get
const get = curry((key, data) => data[key]);
// concat
const concat = curry((symbol, data) => data.concat(symbol));
// map
const map = curry((transformer, data) => data.map(transformer));

fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then(sort((a, b) => b.address.geo.lat - a.address.geo.lat))
  .then(map(get("username")))
  .then(map(concat("!")))
  .then(console.log)
  .catch(console.error);
```

先將排序那段程式包成函式

```javascript
const sortLatitude = sort((a, b) => b.address.geo.lat - a.address.geo.lat);
```

接下來用 `compose` 將改寫

```javascript
// index.js
const responseHandler = compose(
  map(concat("!")),
  map(get("username")),
  sortLatitude
);

fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then(responseHandler)
  .then(console.log)
  .catch(console.error);
```

接下來還可以在一個地方進行優化

```javascript
compose(..., map(concat(!)), map(get('username')), ...)
// 同等於
compose(..., map(compose(concat(!), get('username'))), ...)
```

所以重構的最終版本終於出來了!

```javascript
// index.js
const responseHandler = compose(
  map(compose(concat("!"), get("username"))),
  sortLatitude
);

fetch("https://jsonplaceholder.typicode.com/users")
  .then((r) => r.json())
  .then(responseHandler)
  .then(console.log)
  .catch(console.error);
```

### 小結

Function Composition 就像是樂高一樣，每塊樂高積木雖然只有單一形狀，但卻可以組出一個複雜的模型，而這個概念是 Functional Programming 的核心!
