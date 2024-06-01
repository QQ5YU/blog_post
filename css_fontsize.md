---
title: CSS Fontsize 筆記
date: "2021-08-11"
tags: css
---

# CSS Font size 屬性

###### tags: `CSS`

## font-size 分類

- 關鍵字(含絕對值及相對值)
- 絕對單位(absolute unit)
- 相對單位(relative unit)

---

## 關鍵字

> 屬於絕對尺寸的一種，對應到 px 的尺寸

#### 關鍵字部分有以下幾種

|                            絕對大小值                            |                       相對大小值                       |
| :--------------------------------------------------------------: | :----------------------------------------------------: |
|                     類似於 h1~h6 標籤的概念                      | 比父元素的字體大或小，使用與上面的關鍵字的相近縮放比率 |
| xx-small / x-small / small / medium / large / x-large / xx-large |                    smaller / larger                    |

---

## 絕對單位(absolute unit)

- px (1pixel = 1/96in)
- pt (1point = 1/72)
- pc (1picas = 12pt)
- cm
- mm
- in (1inch = 96px = 2.54cm)

---

## 相對單位(relative unit)

- % (百分比)
- ex (字體的英文小寫 x 高度)
- em (會一直繼承父層，等於所有父層的值相乘)
- rem (root 的字級)
- vw (view port width 相對於視窗的寬)
- vh (view port height 相對於視窗的高)
- vmin (view port min size 相對於視窗的短邊)
- vmax (view port max size 相對於視窗的長邊)

---

## Font-size 設定須知

電腦版本的 chrome 瀏覽器有字級大小的限制，其最小尺寸不得小於 9px，一旦文字設定小於 9px 的字級，那麼該字級就會固定在 9px。