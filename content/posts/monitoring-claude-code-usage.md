---
date: '2026-02-27T03:07:40+08:00'
title: '告別手動查詢——用 ccusage 監控 Claude Code 用量'
tags: [""]
---

# 監控 Claude Code 用量的折騰之旅

> **2026-03-03 更新：** 用了一個禮拜的 Claude Code 之後，我發現當初為了監控用量根本不值得花時間去研究——因為最佳解就是直接升級方案啊！Pro 方案大概一到兩個小時就會把額度用光，與其在那裡乾等浪費時間，直接升上 Max 方案不就什麼問題都解決了嗎？決定還是把 ccusage 拿掉，改用官方建議的方式裝 Status Line 就好了。

---

## 用量問題的發現

最近用了 Claude Code 一陣子，說實話就才幾天，卻發現一個現象：如果認真使用的話，Pro 方案的用量會非常快速地耗盡，不用一兩個小時就可以用光。難怪這麼多人都要訂閱 Max 方案，看來這背後的需求是有實際根據的。

## 找尋解決方案

既然用量消耗這麼快，在還沒認輸前跳到 Max 方案，我當然想找一個方便的方法來監控目前的使用狀況。每次都要手動輸入 `/usage` 才能看到數據，實在太麻煩了。

一開始，我對官方提供的 [Status Line](https://code.claude.com/docs/en/statusline) 範例頗感興趣，想說或許能在裡面加入用量資訊。可惜的是，官方的 Status Line 範例並沒有提供 Usage 的相關資訊——看來這不在官方的預設功能裡。

## 意外發現的方案

在 Google 上茫茫搜尋了一陣子之後，終於找到了一個可行的解決方案——**[ccusage](https://ccusage.com/guide/statusline)**。

## 安裝步驟

安裝上也不困難。我使用 asdf 來管理 Bun 的版本：

```bash
asdf plugin add bun
asdf install bun latest
asdf set -u bun latest
```

## 配置設定

接著在 Claude Code 的設定檔中加入 Status Line 配置。暫時先不客製化太多細節，就用預設的設定就好了：

```json
{
  "statusLine": {
    "type": "command",
    "command": "bun x ccusage statusline",
    "padding": 0
  }
}
```

## 結語

有了這個工具之後，就能在 Claude Code 介面上直觀地看到目前的用量狀況，再也不用每次都手動查詢了。

