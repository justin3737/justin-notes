---
slug: js-recursion
title: JS - 遞迴函數
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=38043a6390fdf82e3a2058d5a76e44345f8f6327&v=4
tags: [javascript]
---

這個函數本身會不斷的呼叫自己，一直到滿足終止的條件為止。

```javascript
function recursive() {
  if (condition) {
    // 停止調用自身
    // ...
  } else {
    recursive();
  }
}
```

### 寫一個簡單的遞迴函數

```javascript
function countDown(number) {
  console.log(number);
}

// 假如每次都減一，我希望達成的結果如下
// 3
// 2
// 1
```

```javascript
function countDown(number) {
  console.log(number);
  countDown(number - 1);
}
countDown(3);
```

如果沒有設置終止條件,會發生錯誤

```
Uncaught RangeError: Maximum call stack size exceeded.
```

我們稍微修改一下：

```javascript
function countDown(number) {
  console.log(number);
  if (number > 0) {
    countDown(number - 1);
  }
}
countDown(3);
```

輸出

```
3
2
1
```

### 計算 N 個自然數之間的和

假設需要使用遞迴函數計算 1 加到 n 的自然數總和 可以定義一下 `sum()`遞迴函數

```javascript
sum(n) = n + sum(n-1);
sum(n-1) = n-1 +sum(n-2);
...
sum(1) = 1;
```

```javascript
function sum(n) {
  if (n <= 1) {
    return n;
  }
  return n + sum(n - 1);
}
```

### 結論

- 遞迴函數是一個函數，他會調用到自己直到不被調用為止。
- 遞迴函數總有一個條件來阻止函數調用自身
