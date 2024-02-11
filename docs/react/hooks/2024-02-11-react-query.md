---
slug: react-query
title: React Query
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [react, hooks, react-query]
---

### React Query V3 簡易說明及使用

What’s React Query? React Query 是一個很適合用於 React Hook 的 Library
他可以幫我們取得、同步、更新、緩存我們的遠端數據，可以更好的處理 Server 端的 State

#### 原理：

透過 Promise、Async、Await 來處理異步數據

#### 優點：

- 幫助我們減少複雜的程式碼,用較少的 React 查詢邏輯來代替
- 不用擔心如何連接新的 Server State
- 節省及提高效能
- 可以處理麻煩的 Cache （Cache Time 預設 5min 回收）
- 不用進行配置

### 簡易使用方式

#### Provider 建置

```javascript

import { QueryClient, QueryClientProvider } from 'react-query';

const queryClient = new QueryClient();
const App = () => {
	return {
		<QueryClientProvider client={queryClient}>
			<div className="app">
				{//... pages here }
			</div>
		</QueryClientProvider>
	}
}
```

#### Basic Usage

```javascript
import { useQuery } from "react-query";

// 建立基本查詢的function getUsers
const getUsers = async () => {
  const response = await fetch("XXX.url.com/users");
  const data = await response.data();
  return data;
};
// 使用 useQuery, getUsers 放到第二個參數
// 除了拿到 data 以外 還有提供 isLoading isError isSuccess 等各種狀態
const { isLoading, isError, isSuccess, data } = useQuery(["users"], getUsers, {
  staleTime: 5000,
  cacheTime: 5000,
});
```

- `staleTime` 是從 Server 獲取資料過時之前的時間長度。 React Query 中的預設值是 staleTime: 0 - 這表示您的資料立即過時！
- `cacheTime` 是從快取中刪除非活動資料之前的時間長度。 React Query 中的預設值為 cacheTime: 300000 - 即 5 分鐘。

### Reference:

- [Why cacheTime in React Query should always be bigger than staleTime](https://www.codemzy.com/blog/react-query-cachetime-staletime)
- [React Query V5](https://tanstack.com/query/latest/docs/framework/react/guides/queries)
