---
title: NextJS 13 筆記
date: "2023-07-02"
tags: nextjs
---

## 建立專案

```javascript!
npx create-next-app
// 指定版本
npx create-next-app@13.1.6
```

###### 回答下面問題

```javascript!
What is your project named? name
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like to use `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to customize the default import alias? No / Yes
```

> customize the default import alias > 可參考文章 https://github.com/vercel/next.js/discussions/46575

## 啟動專案

```javascript!
npm run dev
```

---

## 檔案結構

- **page.tsx**
  新的 route 的首頁
- **layout.tsx**　
  將一個 component 放入進去可同時讓多個頁面使用，且==狀態不會改變(切換頁面時不會重新渲染)==，常用於導覽列。
  > 須注意 RootLayout 頭尾必須有 html 及 body 標籤包住。
- **template.tsx**
  功能與 layout 相似，但==不保留狀態(切換頁面時會重新渲染)==
- **error.tsx**
  畫面渲染有問題時顯示的頁面
- **loading.tsx**
  畫面還沒完成渲染時顯示的頁面
- **not-found.tsx**
  status code 為 404 時顯示的頁面，只有 layout 會在此頁面生效
- **head.tsx(Nextjs 13.2 以下適用)**
  放置\<head>\</head>標籤的內容物
  Ex. \<title>\</title> 、<meta />、<link />...
  > 13.2 版本中引入 metadata API 取代 head

###### metadata 用法

```jsx!
import { Metadata } from "next";
export const metadata: Metadata = {
  title: 'Create Next App',
  description: 'create next app',
}
```

```htmlembedded!
<!-- output -->
<head>
    <title>Create Next App</title>
    <meta name="description" content="create next app">
</head>

```

[metadata 文章](https://juejin.cn/post/7222179242956095543#heading-5 "Next.js 為什麼使用metadata取代了Head組件？")

---

## Routing - file-system based

Next.js 12
: 使用 pages 資料夾進行路由

Next.js 13
: 使用 app 資料夾進行路由

> 目錄在 Next 13 中仍然有效，但同一個 URL 路徑不能同時在兩個文件夾出現。

### Route group

**Step 1**
創建一個資料夾，命名食用()包住。Ex.(names)
**Step 2**
在(names)資料夾下放入其他資料夾作為 routing

#### Example

![Route group](https://i.imgur.com/I30hGCu.png)

##### 如何實作?

創建一個資料夾(auth)，在資料夾下分別創建兩個 route ， login 與 sign-up，底下分別有各自的 page、layout..等等。

> 也可以在(auth)資料夾內寫一個 layout 來讓所有 route 的頁面共用

##### 如何進入 login 及 sign-up 頁面?

只要在 http://localhost:3000/ 後接上 login 或 sign-up 即可，無須接上(auth)。

> X => http://localhost:3000/(auth)/login
> O => http://localhost:3000/login

### Dynamic Routes

**Step 1**
創建一個資料夾，命名食用[]包住。Ex.[postId]
**Step 2**
在[]資料夾內創建一個 page，並可透過 params 取得

###### Example

```jsx!
// [postId]下的page.tsx

import React from 'react'

type Params = {
    params: {
        postId: string
    }
}

export default function postDetail({params: {postId}}: Params) {
  return (
    <div>
        <h1>{postId}</h1>
    </div>
  )
}
```

[App Router 文章]("https://juejin.cn/post/7247044423024721980" "視覺指南：Next.js 13 中的App Router")

### Catch all Segments

> nextjs 會把你在瀏覽器上輸入的東西存到 params 內

#### 使用方法

創建一個資料夾，命名為[...folderName]

##### Example

資料夾結構: src/app/about

先在 about 資料夾下創立一個資料夾命名為[...product]

> Route = app/about/[...product]/page.tsx

若網址為 http://localhost:3000/about/phone/mouse

> params = about/後面輸入的東西

此範例的 params 為 { product: [ 'phone', 'mouse' ] }

---

## 頁面轉跳

### Link Component

> component 內只接受一個 child node，而且只能是 string 或 \<a>\</a>

```jsx!
import Link from 'next/link'
export default function Home() {
  return (
    <main className={styles.main}>
      <Link  href="/about">About Page</Link>
    </main>
  )
}
```

### useRouter Hook

```jsx!
// src/app/page.tsx

'use client'

import { useRouter } from 'next/navigation'

export default function Page() {
  const router = useRouter()

  return (
    // 當button按下去後會將頁面導到http://localhost:3000/dashboard
    <button type="button" onClick={() => router.push('/dashboard')}>
    Go to dashboard page
    </button>
  )
}
```

---

### CSR/SSR/SSG/ISR 介紹

- CSR (Client Side Rendering) - 將渲染的所有過程都讓瀏覽器端處理
  - 優點
    - 使用者感受比較好(不會有畫面閃爍問題)
    - 後續渲染畫面無須跟伺服器互動，對伺服器來說負擔較小。
  - 缺點
    - 初次載入速度較慢
    - 動態生成頁面對 SEO 較不利 (爬蟲不會等待資料請求完成才抓取頁面內容)
- SSR (Server Side Rendering) - 透過伺服器處理資料，編譯成 HTML 檔回傳
  - 優點
    - 資料是動態更新的，可隨時看到最新資料
  - 缺點
    _ 使用者體驗較差(切換頁面時畫面閃爍問題)
    _ 伺服器需一直處理使用者發送的請求，負擔較大
    > 適用於資料更新頻率高，需呼叫 API 取得最新資料的網站
- SSG (Static Side Generation) - 專案在 build 時生成靜態 HTML(包含所有資料)，不需要在伺服器上進行渲染

  - 優點
    - 有利於 SEO
    - 可以讓 HTML 被 cache
  - 缺點
    _ 無法即時更新資料
    _ 更新網站需要花費較多的時間與資源 (整個專案重新 build )
    > 適用於資料變動頻率較低的網站

- ISR (Incremental Static Regeneration) - 專案在 build 時生成 HTML，可設定 catch 時間，當 cache 過期後，使用者發出請求時，伺服器會先回傳先前的檔案，同時重 build 該頁面，等待下次發送
  - 優點
    - 減少從伺服器取得回應的時間
    - 在不需要重新 build 整個專案的情況下，達到靜態頁面的更新
  - 缺點
    - 無法即時更新資料

[CSR/SSR/SSG 參考文章]("https://devmvpchen.com/posts/rendering-pattern" "CSR、SSR、SSG:你需要知道的三種網頁渲染方式")

[ISR 參考文章]("https://pjchender.dev/react/nextjs-doc-basic-feature/" "[NextDoc] Basic Feature 閱讀筆記")

## Fetch Data

##### \<Suspense> build-in react component

```jsx!
<Suspense fallback={<Loading />}>
  <SomeComponent />
</Suspense>
```

Props

- children
  - 主要顯示的 UI
- fallback
  - 傳入一個 UI component，在實際頁面還沒載入完成時會顯示出來。Ex.載入中轉圈圈的畫面

## Pre-rendering & hydration

- Pre-rendering
  - 意指 NextJs 會預先將 HTML 產出及該頁面所需最少的 javascript code
- hydration
  - 當頁面在瀏覽器執行完後開始執行 javascript 程式碼，使頁面可以互動，這個過程稱之為 hydration

## Sever vs Client Component

> NextJs 13 ( 使用 app router ) 預設採用 server component

### Client component

> fetch & render 在 client 端

#### 使用方法

###### 在頁面最上方加上 use client

```jsx!
"use client"
```

#### 使用時機

- 使用 React Hook
- 使用 React class component
- 使用瀏覽器內建的 API 或存取瀏覽器相關的資訊像是 onClick()、window..

### Server component

> fetch & render 在 server 端

#### 使用方法

###### 預設即是 server component

#### 使用時機

- 直接存取後端的資料
- fetch data
- 儲存一些重要的資訊 Ex. access tokens / API Key ..

[Server Components vs. Client Components](https://blog.bitsrc.io/server-components-vs-client-components-in-next-js-13-617fe96df813 "Server Components vs. Client Components in Next.js 13")

[When to use Server and Client Components?](https://nextjs.org/docs/getting-started/react-essentials#when-to-use-server-and-client-components "When to use Server and Client Components? NEXT.JS")
