---
date: '2025-11-22T05:43:00+08:00'
draft: false
title: '不用連接 Kobo 裝置，直接從 Mac 匯入閱讀筆記到 Obsidian'
tags: ["Life"]
---


## Obsidian 是什麼？

[Obsidian](https://obsidian.md) 是一個免費且彈性的筆記應用程式，專為個人思考而設計。你可以用它來寫日記、建立知識庫，或是管理專案。它提供無限的畫布讓你整理想法，並支援數千種外掛程式，讓你能客製化自己的思考空間。

## Obsidian Kobo Highlights Import 插件

[Obsidian Kobo Highlights Import](https://github.com/OGKevin/obsidian-kobo-highlights-import) 是一個專門用來匯入 Kobo 電子書劃線和筆記的 Obsidian 插件。它可以將你在 Kobo 上的閱讀筆記直接整合到 Obsidian 筆記系統中，還支援自訂模板，讓你完全掌控輸出格式。

## 不需要連接裝置的新方法

網路上大部分的文章都是教你怎麼從 Kobo 的電子書裝置，把筆記匯到 Obsidian 裡面，甚至連插件官方文件自己也是這麼教的。官方的標準步驟是：

1. 用 USB 線連接 Kobo 裝置到電腦
2. 確認裝置已掛載（或手動掛載）
3. 開啟插件的匯入視窗
4. 在 `.kobo` 隱藏資料夾中找到 `KoboReader.sqlite` 檔案
5. 執行匯入

但其實後來我才發現這個 `KoboReader.sqlite`（或 `Kobo.sqlite`）的檔案，如果你在 Mac OS 的電腦上安裝 Kobo 的閱讀程式 [Kobo Desktop](https://help.kobo.com/hc/zh-tw/articles/360020121953-在-PC-或-Mac-上安裝-Kobo-Desktop) 的話，也會有。也就是說，**其實我們可以把筆記直接從 Mac OS 上輸出，而不需要接 Kobo 電子書裝置**。

## 實際操作步驟

### 準備工作
1. **安裝 Obsidian Kobo Highlights Import 插件**
   在 Obsidian 中搜尋並安裝此插件
2. **安裝 Kobo Desktop**
   從 [Kobo 官方網站](https://help.kobo.com/hc/zh-tw/articles/360020121953-在-PC-或-Mac-上安裝-Kobo-Desktop) 下載並安裝

### 匯入步驟（不連接裝置）
1. 開啟插件的匯入視窗（點擊側邊欄圖示或使用 Command Palette）
2. 選擇「Import from Kobo」
3. **手動選擇 Kobo Desktop App 的資料庫檔案**

### macOS 上的資料庫位置

資料庫檔案位於：

```
/Users/[你的使用者名稱]/Library/Application Support/Kobo/Kobo Desktop Edition/Kobo.sqlite
# or
/Users/[你的使用者名稱]/資源庫/Application Support/Kobo/Kobo Desktop Edition/Kobo.sqlite
```

### 顯示隱藏檔案

比較麻煩的是 `Kobo.sqlite` 是隱藏檔案。你可以使用熱鍵顯示所有隱藏檔：

**Command + Shift + .**

### 重要提示

- 這個 `Kobo.sqlite` 檔案就等同於 Kobo 裝置上的 `Kobo.sqlite`
- 只要你有使用 Kobo Desktop App 並且已經同步過書籍，所有的劃線和筆記都會在這個檔案裡
- **不需要連接實體 Kobo 裝置**

## 結語

這個方法讓使用 Mac 的讀者可以更方便地管理閱讀筆記，不用每次都要把 Kobo 裝置接上電腦。只要確保 Kobo Desktop 有同步資料，就能隨時把最新的筆記匯入到 Obsidian 中。
