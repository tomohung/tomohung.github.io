---
date: '2026-02-07T00:49:36+08:00'
title: 'YouTube 音檔下載方案：yt-dlp'
tags: ["Web"]
---

## 原本的痛點

ChatGPT 橫空出世以來，我在 YouTube 上想要學習的影片，因為可以拿到字幕檔來做筆記變得很方便。

但如果沒有字幕檔就變得很麻煩。

原本我會用 4K Video Downloader 來下載 YouTube 的聲音檔。但這個工具有個讓人困擾的問題：**穩定性不佳**。

有時候沒更新就無法使用，有些影片也會莫名其妙下載失敗。對於興致來了，正需要使用的時候，這種時靈時不靈的狀況真的很惱人。

## 我的學習流程

其實我的需求很單純，整個流程是這樣的：

1. 下載 YouTube 影片的聲音檔
2. 上傳到 Notebook LM，讓它生成字幕
3. 把字幕檔交給 AI 整理成易讀格式
4. 開始學習吸收內容

這是我目前最習慣的學習方式，但關鍵就卡在第一步——下載聲音檔的穩定性。

## 新發現：yt-dlp

最近意外發現了 **[yt-dlp](https://github.com/yt-dlp/yt-dlp)** 這個工具，試用之後覺得非常簡單好用！[^1]

### 安裝方式

如果用 macOS，可以透過 Homebrew 安裝：

```bash
brew install yt-dlp
```

**重要：** 如果要轉換成 MP3 格式，還需要安裝 ffmpeg：

```bash
brew install ffmpeg
```

### 使用方法

下載 YouTube 影片的音檔非常簡單：

```bash
yt-dlp -x --audio-format mp3 "https://www.youtube.com/watch?v=VIDEO_ID"
```

這個指令會：
- `-x`：只提取音檔
- `--audio-format mp3`：轉換成 MP3 格式（需要 ffmpeg）
- 最後接上 YouTube 影片網址

### 設定快捷指令

每次都要輸入 `yt-dlp -x --audio-format mp3` 有點囉嗦，可以設定一個 zsh alias 來簡化操作。

編輯 `~/.zshrc` 檔案，加入這一行：

```bash
alias mp3='yt-dlp -x --audio-format mp3'
```

之後就可以直接用 `mp3` 指令，而且可以一次下載多個影片聲音檔：

```bash
mp3 https://www.youtube.com/watch?v=VIDEO_ID_1 https://www.youtube.com/watch?v=VIDEO_ID_2
```

Easy!

**2026-02-15 Update**

下載 mp3 給語意辦識用可以品質不用那麼好,檔案就可以再縮小一些

```bash
alias mp3='yt-dlp -x --audio-format mp3 --postprocessor-args "ffmpeg:-ar 16000 -ac 1 -b:a 64k"'
```

## 完整的學習工作流

有了穩定的音檔下載工具後，整個流程就順暢多了：

1. **下載音檔**：用 yt-dlp 快速下載 YouTube 影片的聲音
2. **生成字幕**：上傳到 Notebook LM，很快就能拿到逐字稿
3. **AI 整理**：請 AI 幫忙順稿，整理成容易閱讀的格式
4. **開始學習**：有了結構清晰的文字內容，就可以好好吸收了

[^1]: https://github.com/yt-dlp/yt-dlp
