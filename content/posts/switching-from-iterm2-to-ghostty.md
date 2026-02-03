---
date: '2026-02-03T17:36:35+08:00'
title: '終端機換血：從 iTerm2 到 Ghostty'
tags: ["Web"]
---

今天因為不斷看到好多人分享一篇 Claude Code 負責人的使用密訣，其中提到 Anthropic 團隊幾乎所有人都是用 Ghostty，這引起了我的好奇，決定來試試看能不能取代我現在在用的 iTerm2。[^1]

## 安裝：簡單到不可思議

其實在 macOS 上安裝意外的簡單，使用 Homebrew 就可以了：

```bash
brew install --cask ghostty
```

結束 XD

## Zero Configuration 的哲學

為什麼這麼簡單？因為 Ghostty 號稱 zero configuration，意思就是它的預設值已經符合最多人的使用習慣了。[^2] 這個由 Mitchell Hashimoto 開發的終端機模擬器，主打快速、功能豐富，並且跨平台。[^3]

官方網站：[ghostty.org](https://ghostty.org)

## 分割視窗：告別 tmux

我原來用 iTerm2 + tmux 是為了分割視窗，可以同時看 server console 和 nvim 的內容。但 Ghostty 的分割 panel 也很簡單，而且是內建功能，不需要額外安裝 tmux。

tmux 的 session 還是有其它用途，但現在我不用打開終端機就常駐它了。

## 設定：一個文字檔搞定

要改設定也很簡單，直接按下 `Cmd + ,` 呼叫 preference，會開啟文字的設定檔。

我目前也只改了：

```
theme = Catppuccin Mocha
font-size = 16
```

沒辦法，老人家視力看預設的 13 還是有點小 XD

## 實用小技巧

使用 `Cmd + Shift + P` 呼叫命令列，搜尋指令 `Equalize Splits`，就可以把這些 Splits 平均大小。

## 為什麼選擇 Ghostty

看到 Anthropic 團隊內部廣泛使用 Ghostty，並且在他們的文章中提到，很多工程師用它來執行 Claude Code，進行程式碼審查、除錯、原型開發等工作，讓我對這個工具更有信心。[^1] 對我來說，能夠簡單快速地完成終端機的設定，又能保持高效能，就是最好的工具。

[^1]: 原文在 X 上分享但沒登錄的話看不到，有熱心的網友備份 https://ykdojo.github.io/claude-code-tips/content/boris-claude-code-tips
[^2]: https://github.com/ghostty-org/ghostty
[^3]: https://mitchellh.com/ghostty


