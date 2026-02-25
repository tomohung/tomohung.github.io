---
date: '2026-02-25T10:39:25+08:00'
title: '在 Ghostty 中優雅地切換分割畫面焦點'
tags: ["Web"]
---

最近的工作環境是用 [Ghostty](/posts/switching-from-iterm2-to-ghostty/) 把畫面分割成兩半——左邊跑 Terminal + Claude Code，右邊開 Neovim。這種配置用起來挺順手的，但在切換分割畫面焦點這件事上，折騰了一小段時間。

## 從預設快捷鍵說起

Ghostty 預設是用 `cmd + 方向鍵` 來移動焦點，功能上沒什麼問題，但用久了總是會想起以前用 tmux 時的肌肉記憶——`ctrl + h/j/k/l` 那套 Vim 風格的操作。

於是去找了一下設定方式，發現可以直接在 Ghostty 的設定檔裡加上 keybind：

```
keybind = ctrl+h=goto_split:left
keybind = ctrl+j=goto_split:bottom
keybind = ctrl+k=goto_split:top
keybind = ctrl+l=goto_split:right
```

改完之後，在 Ghostty 層級切換分割畫面確實沒問題了。

## 但問題來了

焦點在 Neovim 上的時候，Neovim 裡我也設定了 `ctrl + h/j/k/l` 來切換它自己內部的分割視窗（window）。結果這些按鍵全部被 Ghostty 攔截了，根本傳不進 Neovim。

想了一下，這麼經典的 Vim 使用情境，不可能只有我遇到，肯定有人解過這個問題。

## 解法：`performable` 前綴

果然，在 Ghostty 的 [GitHub Discussions](https://github.com/ghostty-org/ghostty/discussions/7381) 上找到了答案。只要在 keybind 前面加上 `performable:` 前綴即可：

```
keybind = performable:ctrl+h=goto_split:left
keybind = performable:ctrl+j=goto_split:bottom
keybind = performable:ctrl+k=goto_split:top
keybind = performable:ctrl+l=goto_split:right
```

`performable` 的意思是：**只有在這個動作實際上可以被執行時，才攔截這個按鍵**。

以切換分割焦點為例，如果目前焦點在 Neovim 上，而且是要往「右邊」切換，但右邊根本沒有其他 Ghostty 分割視窗，Ghostty 就會判斷這個動作「無法執行」，於是把按鍵乖乖地傳給 Neovim 處理。

這樣一來，Ghostty 層級和 Neovim 層級的分割視窗切換就可以和平共存，互不干擾。

Nice!
