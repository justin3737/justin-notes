---
slug: vite-react-shadcn-ui
title: Vite React Shadcn UI 開發筆記 - 設定篇
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=38043a6390fdf82e3a2058d5a76e44345f8f6327&v=4
tags: [vite, shadcn-ui, css, react, tailwind]
---

### 基本安裝設定 (Vite)

```
npm create vite@latest my-project -- --template react
cd my-project
```

### 接著下載 Tailwind CSS 以及相關的套件，並生成 config 檔案

```
npm install -D tailwindcss postcss autoprefixer

npx tailwindcss init -p
```

### 在你的 tsconfig.json 檔案中新增以下設定，確保解析元件路徑時不會出錯

```
"baseUrl": ".",
"paths": {
  "@/*": ["./src/*"]
}
```

### 更新你的 vite.config.ts 檔案，確保解析元件路徑時不會出錯

```
# (so you can import "path" without error)
npm i -D @types/node
```

```
import path from "path"
import react from "@vitejs/plugin-react"
import { defineConfig } from "vite"

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
})
```

### 執行 shadcn-ui CLI 指令來初始化 Shadcn ui

```
npx shadcn-ui@latest init
```

### 根據需求回答以下問題以完成初始化

```
Would you like to use TypeScript (recommended)? no / yes
Which style would you like to use? › Default
Which color would you like to use as base color? › Slate
Where is your global CSS file? › › src/index.css
Do you want to use CSS variables for colors? › no / yes
Where is your tailwind.config.js located? › tailwind.config.js
Configure the import alias for components: › @/components
Configure the import alias for utils: › @/lib/utils
Are you using React Server Components? › no / yes (no)
```

### 完成設定後就能開始新增元件

```
// 新增button
npx shadcn-ui@latest add button
```

```
// 我們可以在頁面中 import 剛剛新增的 Button 元件如下：

import { Button } from "@/components/ui/button"

export default function Home() {
  return (
    <div>
      <Button>Click me</Button>
    </div>
  )
}
```
