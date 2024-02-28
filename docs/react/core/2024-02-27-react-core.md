---
slug: react-core
title: React 設計原理
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=38043a6390fdf82e3a2058d5a76e44345f8f6327&v=4
tags: [react]
---

一些常常聽到的 `React` 專有名詞, 本篇以 18 版為主：

> - 什麼是 Concurrent Mode?
> - 什麼是 Fiber?
> - 什麼是 Reconciliation?
> - 什麼是 Suspense?

### 什麼是 Concurrent Mode

`React` 16 時，`React Team` 曾經把整個框架重新寫過，這個「`Fiber`」耗時一年多才完成，其實就是為了 `Concurrent Mode` 所鋪的路。

### 什麼是 Fiber

為了讓 `Render` / `Reconciliation` 的過程更加彈性：`React Team` 決定把這個過程一次搞定 `Render` 整個 `Tree` 的步驟切成一個個更小的步驟，讓整個過程可以暫停、也可以放棄也可以併發 (`Concurrent`) 的執行

或許大部分的 `React` 開法者都遇過再輸入框打字時, 因為 `state` 改變造成 `Render` 而擋住了輸入框的立即更新, 這個互動常常會讓使用者覺得卡卡的,這個就是因為中斷、暫停 `Render` 能解決的問題

而 `Fiber` 就是 `Render` / `Reconciliation` 的最小單位

### 什麼是 Reconciliation

`Reconciliation` (協調)是指 `React` 內部用來比較新舊虛擬 `DOM Tree`，找出實際 DOM 中進行更新的過程。
`Reconciliation` 是 `React` 強大性能的關鍵之一，他通過高效的比較和更新 `DOM` 元素來減少實際 `DOM` 操作，因爲實際的 `DOM` 操作通常是性能成本更高的操作。當組件的狀態 `state` 或 屬性 `props` `發生變化時，React` 會去創建新的虛擬 `DOM Tree`, 與上次的虛擬 `DOM Tree` 做比較確定有哪些地方需要更新, 這個過程也叫做「 對比差異 」(Diffing)。

### 什麼是 Suspense?

Suspense 是一個讓資料取得以前，還沒準備好的狀態下, 可以 `Render UI` 顯示為 `Loading` 狀態。

```javascript
const ProfilePage = React.lazy(() => import("./ProfilePage"));

// Show a spinner while the profile is loading
<Suspense fallback={<Spinner />}>
  <ProfilePage />
</Suspense>;
```

### Reference

- [理解 React 的下一步：Concurrent Mode 與 Suspense](https://chentsulin.medium.com/%E7%90%86%E8%A7%A3-react-%E7%9A%84%E4%B8%8B%E4%B8%80%E6%AD%A5-concurrent-mode-%E8%88%87-suspense-327b8a3df0fe)
