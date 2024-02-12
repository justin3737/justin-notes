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

### Why React Query ?

[為什麼不應該在 React 中直接使用 useEffect 從 API 中直接獲取數據](https://blog.skk.moe/post/why-you-should-not-fetch-data-directly-in-use-effect/)

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
      {/*... pages here */}
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
const { isLoading, isError, isSuccess, data } = useQuery(["users"], getUsers);
```

### React Query Keys

```javascript
import { useQuery } from "tanstack/react-query";

// 建立基本查詢的function getUsers
// 把querykey 帶入
const getUsers = async ({ querykey }) => {
  const response = await fetch(`https://xxx`, querykey[1]);
  return response.json();
};

// 設定 queryKey...
const [id, setId] = useState(0);

const { isLoading, isError, isSuccess, data, isFetching } = useQuery(
  ["users", id],
  getUsers,
  {
    //更多設定...
    staleTime: 0, // 從Server 獲取資料後過時的長度
    enable: false, //是否啟用, 如果有相依某api, 可以等待某api回來以後再來觸發
    refetchOnWindowFocus: false, // 離開視窗 再回來並不會再打一次 request
    cacheTime: 1000, // 一秒後清除cache資料
    retry: 10, // 重試次數
    retryDelay: 1000, // 等待1000 ms 在重試
  }
);
```

- `staleTime` 是從 Server 獲取資料過時之前的時間長度。 React Query 中的預設值是 staleTime: 0 - 這表示您的資料立即過時！
- `cacheTime` 是從快取中刪除非活動資料之前的時間長度。 React Query 中的預設值為 cacheTime: 300000 - 即 5 分鐘。

### useMutation

```javascript
useMutation(addTodo, {
  onMutate: (variables) => {
    // A mutation is about to happen!

    // Optionally return a context containing data to use when for example rolling back
    return { id: 1 };
  },
  onError: (error, variables, context) => {
    // An error happened!
    console.log(`rolling back optimistic update with id ${context.id}`);
  },
  onSuccess: (data, variables, context) => {
    // Boom baby!
  },
  onSettled: (data, error, variables, context) => {
    // Error or success... doesn't matter!
  },
});
```

### Invalidation from Mutations

```javascript
// 使用invalidateQueries 重新refetch資料
queryClient.invalidateQueries("key");
```

### Tutorial Video:

- [基本使用](https://www.youtube.com/watch?v=2ITjyeNFFuo&ab_channel=WeiWei)
- [useMutation](https://www.youtube.com/watch?v=TCdR6KifeKs&ab_channel=WeiWei)

### Reference:

- [React Query 介紹](https://medium.com/@weiyun0912/react-query-%E4%BB%8B%E7%B4%B9-e97b8c9e7823)
- [Why cacheTime in React Query should always be bigger than staleTime](https://www.codemzy.com/blog/react-query-cachetime-staletime)
- [React Query v5](https://tanstack.com/query/latest/docs/framework/react/guides/queries)
- [React Query keys v5](https://tanstack.com/query/latest/docs/framework/react/guides/query-keys)
- [Invalidation from Mutations](https://tanstack.com/query/latest/docs/framework/react/guides/invalidations-from-mutations)
