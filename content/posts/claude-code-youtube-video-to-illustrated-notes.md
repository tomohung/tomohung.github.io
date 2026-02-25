---
date: '2026-02-25T21:57:56+08:00'
title: '用 Claude Code 把 YouTube 影片整理成圖文並茂的筆記'
tags: ["Web"]
---

距離上次寫程式又過了一段時間。上一次是用 Cursor 的月費方案把需求做完，之後就暫時擱置了。這禮拜又有新的需求冒出來，簡單評估一番之後，決定來試試需要付費的 **[Claude Code](https://claude.com/product/claude-code)**。

我知道現在入坑算是有點晚了，不過測試完的結果讓我真的有點興奮。

---

## Agent 的能力，才是關鍵

Agent 在背後自動執行指令的概念並不新鮮，但 Agent 本身的能力，才是決定事情能不能真正成事的關鍵。接下來我想用一個今天實際做的例子，來說明 Claude Code 到底有多厲害。

---

## 原本的工作流程

[這個情境我之前提過](/posts/switching-to-yt-dlp-for-youtube-audio/)。我平常的習慣是這樣的：

1. 用 **[yt-dlp](https://github.com/yt-dlp/yt-dlp)** 下載 YouTube 影片的音訊
2. 丟進 **[MacWhisper](https://goodsnooze.gumroad.com/l/macwhisper)** 轉成逐字稿
3. 再用 **AI** 整理成易讀格式，並加上重點摘要

這樣在看影片之前，可以先把文字內容掃過一遍，對主題有個底，看影片時也更容易掌握脈絡。

不過，純文字的整理畢竟少了點什麼——沒有圖片，視覺上就是差了一截。

---

## 新的想法：讓 Claude Code 串起整個流程

這次試用完 Claude Code 之後，我有了一個不一樣的做法。整體概念如下：

### Step 1 — 下載影片與字幕

找到 YouTube 影片的 URL 之後，同樣用 **[yt-dlp](https://github.com/yt-dlp/yt-dlp)** 下載。這次下載的不只是音訊，而是完整的影片檔。

```bash
alias video='yt-dlp -f "bestvideo[height<=1080][ext=mp4]+bestaudio[abr<=64]/best[height<=1080]"'
```
就可以下載影片

```bash
video <Youtube URL>
```

### Step 2 — MacWhisper 批次輸出含時間軸的字幕

[MacWhisper](https://goodsnooze.gumroad.com/l/macwhisper) 可以設定 **Batch Export**，這次我要輸出的格式是 **SRT 字幕檔**，也就是包含時間軸的那種，而不是純粹的逐字稿。

### Step 3 — Claude Code 整理逐字稿與重點摘要

Claude Code 先讀入字幕檔，做兩件事：

- 把 SRT 字幕整理成**人類可閱讀的繁體中文格式**，並修正可能的錯字
- 根據整理好的逐字稿，產生第二份**重點摘要**文件

### Step 4 — 偵測關鍵時間點

這裡是整個流程的重頭戲。Claude Code 會重新對照字幕內容與時間軸，用語氣與關鍵詞去判斷哪些時間點可能有重要的視覺資訊，例如出現「這裡」、「按」、「看到」這類指示詞，或是段落切換、講者強調數字與列舉的地方。

最後會輸出一份**關鍵時間點清單**，記錄這些時間位置與對應的畫面推測。

### Step 5 — 用 ffmpeg 自動擷取截圖

有了時間點，Claude Code 接著對影片執行 **[ffmpeg](https://ffmpeg.org)** 指令，把每個關鍵位置的畫面截成圖片，存進 `screenshots/` 資料夾。

```bash
ffmpeg -ss <timestamp> -i "<影片檔名>.mp4" -frames:v 1 -update 1 "screenshots/<檔名>_<時間>_<描述>.png" -y
```

> 小細節：加上 `-update 1` 是必要的，避免 ffmpeg 把含有特殊字元的檔名誤判為序列模式。

### Step 6 — 產生圖文並茂的 Obsidian 筆記

最後一步，Claude Code 把重點摘要與截圖整合成一份 **[Obsidian](https://obsidian.md) 格式的 Markdown 筆記**，截圖會自動插入對應段落的後方，圖文緊密對應。

```
results/
├── <影片名稱>_Obsidian.md
└── screenshots/
    ├── <影片名稱>_01m30s_重點畫面.png
    └── ...
```

---

## 成果與感想

這樣最後拿到手的，就是一份**圖文並茂的影片重點整理**。

當然，截圖的位置是根據字幕語意判斷的，不保證每一張都剛好是最完美的畫面，這是這個方法目前的限制。但和之前純文字的整理相比，有圖片的版本看起來賞心悅目很多，對大腦的刺激也應該會更豐富。

這不代表我從此不看影片，但我相信，未來要回來複習的時候，翻一翻這份圖文筆記，就能很快回想起大部分的內容了。

## 後記

隔天睡覺起床後，覺得是不是應該用 [Groq Speech to Text API](https://console.groq.com/docs/speech-to-text) 把 MacWhisper 的工作也取代掉算了，這樣可以一條龍直接做完。不過稍微研究之後，語音太長也有可能出問題，官方會建議切成小塊分開處理。

做筆記不是每天都要做的事，先不要把事情複雜化 :)
