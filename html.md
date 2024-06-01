---
title: HTML 筆記
date: "2021-05-02"
tags: html
---

# HTML head (Meta / Link) Tag 大補帖

###### tags: `網頁切版`

## 網頁基本設定 tag

### Meta charset

指定網頁內容編碼 "character set" (現在大多數的網頁編碼都是 UTF-8)

```htmlembedded!
<meta charset="utf-8">
<!-- HTMl 4.01版 -->
<meta http-equiv="content-type" content="text/html; charset=utf-8">
```

### Meta viewport

指定瀏覽器怎麼渲染和縮放網頁畫面的寬高

- width=device-width 指定瀏覽器頁面的寬度同裝置 (device) 的寬度
- initial-scale=1.0 指定畫面初始縮放比例為 100%，即不放大也不縮小

```htmlembedded!
<!-- 一般常用方法 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- 防止使用者用手指做畫面的縮放 -->
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1">
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
```

- minimum-scale=1 設定畫面最小的縮放比例為 1
- maximum-scale=1 設定畫面最大的縮放比例為 1
- user-scalable 用來設定是否允許使用者改變縮放比例

### Meta refresh

設定幾秒鐘後跳轉 (redirect) 到某一個 URL

```htmlembedded!
<!-- 載入 5 秒後，自動跳轉到 https://www.google.com -->
<meta http-equiv="refresh" content="5; url=https://www.google.com">
<!-- 不加 url 參數則是定時刷新頁面的意思 content等於間格秒數 -->
<meta http-equiv="refresh" content="10">
```

### Meta robots

控管應如何將各個網頁納入索引並顯示在搜尋引擎的 (Google) 搜尋結果中

- noindex: 不要在搜尋結果中顯示這個網頁
- nofollow: 不要追蹤這個網頁上的連結
- all: 預設值，等同於 index, follow
- none: 相當於 noindex, nofollow
- noarchive: 不要在搜索結果中顯示快取連結
- nosnippet: 不要在搜尋結果中顯示這個網頁的文字摘要或影片預覽畫面
- notranslate: 不要在搜尋結果中提供這個網頁的翻譯
- noimageindex: 不為這個網頁上的圖片建立索引
- unavailable_after: [date/time]: 在指定的日期/時間後不在搜尋結果中顯示這個網頁

```htmlembedded!
<!-- 指示搜尋引擎不要索引這個網頁 -->
<meta name="robots" content="noindex">
<!-- 時有多個條件，用半形逗號分隔開 -->
<!-- 指示搜尋引擎不要索引這個網頁，也不要檢索網頁上的任一連結 -->
<meta name="robots" content="noindex, nofollow">
```

### Meta generator

錄網頁編輯器名稱

```htmlembedded!
<meta name="generator" content="編輯器名稱">
```

### Meta distribution

記錄網頁的發佈地區

```htmlembedded!
<meta name="distribution" content="網頁發佈地區">
```

### Link icon

設定網站 Icon

````htmlembedded!
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon" />


---
## 與搜尋有關的 tag

### Meta Title
用於網頁搜尋時的關鍵字
```htmlembedded!
<title> title | content</title>
````

### Meta Description

用於網頁搜尋後顯示的描述

```htmlembedded!
<meta name="description" content="content">
```

### Meta Keywords

網頁的關鍵字列表，以便搜尋引擎更好地理解網頁內容，從而更好地索引和排名網頁
(現今已經不考慮關簡字來做網頁排名)

```htmlembedded!
<meta name="keywords" content="SEO, 搜尋引擎優化, 行銷策略顧問, 行銷解決方案">
```

---

## 與權益有關的 tag

### Meta author

指定作者或頁面擁有人資訊

```htmlembedded!
<meta name="author" content="w5yuuu">
```

### Meta copyright

版權歸屬

```htmlembedded!
＜meta name=”copyright” content=”w5yuuu”＞
```

---

## 設定分享連結長相的 tag

### Meta og:title

網站的標題

```htmlembedded!
<meta property="og:title" content="網頁標題" />
```

### Meta og:image

網站預覽圖 url

```htmlembedded!
<meta property="og:image" content="預覽圖 URL" />
```

### Meta og:description

描述或摘要

```htmlembedded!
<meta property="og:description" content="描述或摘要" />
```

### Meta og:type

類型

```htmlembedded!
<meta property="og:type" content="article"/>
```

### Meta og:url

網址

```htmlembedded!
<meta property="og:url" content="網址"/>
```

### Meta og:site_name

公司名稱、網站名稱

```htmlembedded!
<meta property="og:site_name" content="公司名稱、網站名稱"/>
```

---

#### Reference

- https://www.tpisoftware.com/tpu/articleDetails/1989
- https://www.fooish.com/html/meta-tag.html
- https://ithelp.ithome.com.tw/articles/10195279