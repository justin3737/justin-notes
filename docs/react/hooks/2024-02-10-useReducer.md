---
slug: react-useReducer
title: 生活中學coding - useReducer 篇
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [react, hooks, "生活中學coding"]
---

### 前言

年底到小區採購生活日用品，發現每天根據不同的信用卡會有不同的優惠，比如週三玉山卡可以 $988 折 $120。當我採購好東西到櫃檯結帳，店員小姐告訴我另一個優惠滿 $1200 又送 $100，於是我又去湊齊金額，但不可計入買一送一商品。

在這樣的過程裡，我開始思考，在程式判斷時有這麼多的複雜結帳情境與商業邏輯，如果寫成 if...else...鐵定會變成又髒又醜又難以維護。

這一篇用 `useReducer` 在不動到太多 UI 程式碼的狀態下，來方便抽換不同邏輯。

### 什麼是 useReducer

根據官網說法：

> An alternative to `useState`. Accepts a reducer of type (state, action) => newState, and returns the current state paired with a `dispatch` method.
> (If you’re familiar with Redux, you already know how this works.)

看一下用法：

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

其概念其實來自 Redux 大概有 80% 像

```
View --> Action --> Dispatch --> Reducer --> 更新View
```

### 先以簡單的加減數字使用 useReducer 表示

這裡先定義一個預設的邏輯 default router

```javascript
// Step:1 reducer: 數字加減的邏輯寫在這裡
const defaultReducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    default:
      throw new Error(`Unhandled type ${action.type}`);
  }
};

// Step:2 組裝成 hooks 提供給頁面使用
const useCounter = (reducer = defaultReducer, initialCount = 0) => {
  const [count, dispatch] = useReducer(reducer, initialCount);
  const onIncrement = useCallback(() => {
    dispatch({ type: "increment" });
  }, []);
  const onDecrement = useCallback(() => {
    dispatch({ type: "decrement" });
  }, []);
  return {
    count,
    onIncrement,
    onDecrement,
  };
};

// Step:3 page中使用 hooks:
const CounterPage = () => {
  const { count, onIncrement, onDecrement } = useCounter();
  return (
    <section>
      <h1>{count}</h1>
      <button onClick={onIncrement}>onIncrement</button>
      <button onClick={onDecrement}>onDecrement</button>
    </section>
  );
};
```

### 定義新的 reducer，特殊的邏輯寫可以在這裡

比如我希望數字 最大值 5 最小值為 0，上面的 `defaultReducer` 是沒有限制數字的上下限。
我們用 `Math.max()` 與 `Math.min()` 函式來決定最大值跟最小值。
除此之外的 `action type` 我們用上面定義的 `defaultReducer` 來處理。

```javascript
const maxReducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return Math.max(state + 1, 5);
    case "decrement":
      return Math.min(state -1, 0);
    default
      return defaultReducer(state, action)
  }
};
```

### 結論：

- `useReducer` 這樣的 design pattern 可以方便抽換各種不同的商業邏輯，也讓後續程式碼維護可以更加輕鬆。
