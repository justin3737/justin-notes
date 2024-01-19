---
slug: vite-react-react-tailwind-css
title: Vite React Tailwind CSS 開發筆記
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: [vite, tailwind, css, react]
---

### 建立專案

```
npm create vite@latest my-project -- --template react
cd my-project
```

### 安裝 Tailwind CSS

```
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### 配置範本路徑

```
// tailwind.config.js

/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### 把 Tailwind CSS 指令加入到專案

```
// index.css

@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 啟動專案

```
npm run dev
```

### Start Coding!!

```
// App.jsx

export default function App() {
  return (
    <h1 className="text-3xl font-bold underline">
      Hello world!
    </h1>
  )
}
```
