---
date: '2026-03-03T21:49:10+08:00'
title: '用 Claude Code 一鍵將 YouTube 影片轉成 Obsidian 筆記'
tags: ["Web"]
---

# 用 Claude Code 一鍵將 YouTube 影片轉成 Obsidian 筆記

上次在[那篇文章](/posts/claude-code-youtube-video-to-illustrated-notes/)的後記才提到要用 Groq 的 API 來取代 Mac Whisper 手動轉逐字稿的動作，想說之後再做——但實際上當天我就忍不住開始著手這個優化流程了。

相較於最近很火紅的 OpenClaw，在建這個把 YouTube 影片轉成圖文並茂筆記的過程中，我深深刻刻地感受到：對開發者來說，CLI 搭配 AI 真的是王道。

---

## 前言

這篇文章要介紹的是一個思路，一個跑在 Claude Code 上的自動化 skill。輸入 YouTube 網址，它會幫你下載影片、語音轉錄、整理逐字稿、擷取關鍵截圖，最後產出一份可以直接匯入 Obsidian 的完整筆記。

---

## 它能做什麼？

貼上一個（或多個）YouTube 網址，這個 skill 會自動：

1. 下載影片（最高 1080p）
2. 用 Groq Whisper API 做語音辨識轉錄
3. 整理成人類可讀的繁體中文逐字稿
4. 萃取重點摘要（條列格式，快速可掃讀）
5. 分析關鍵時間點，用 ffmpeg 擷取對應畫面截圖
6. 用感知雜湊（perceptual hashing）去除重複截圖
7. 將摘要、逐字稿、截圖整合成 Obsidian Markdown 筆記

整個流程全程自動，完全不需要人工介入。

---

## 事前準備

```bash
# 安裝必要工具
brew install ffmpeg czkawka
pip install yt-dlp          # 或 brew install yt-dlp

# 設定 Groq API 金鑰
export GROQ_API_KEY=<your_key>
```

Groq 提供 `whisper-large-v3-turbo` 模型轉錄速度非常快，一小時的影片通常不到一分鐘就能完成。
我實測轉了好幾個小時的音檔，也才花不到1美金，換回來的時間很划算。

---

## 技術細節：為什麼這樣設計？

### 音訊壓縮策略

影片轉成 MP3 時使用 16kHz 取樣率、單聲道、32kbps 位元率：

```bash
ffmpeg -i video.mp4 -ar 16000 -ac 1 -map 0:a -b:a 32k output.mp3
```

這個設定讓一小時的影片壓縮到約 14.4MB，遠低於 Groq 的 25MB 上傳限制，同時對語音辨識精度幾乎沒有影響。

### 逐字稿整理哲學：拼圖組裝

整理逐字稿時遵循「拼圖組裝」原則：

> 你是技術編輯，不是內容創作者。每個原文詞彙都是拼圖，只能組合，不能替換。

這意味著：

* 保留講者原有用語，不用同義詞替換
* 只修正明顯錯字
* 簡體字轉換為繁體中文
* 依語意分段加標題，重要名詞加粗

### 截圖時間點判斷邏輯

系統會從 Groq 回傳的 `segments[]` 時間戳中，分析以下信號來決定截圖時機：

* 字幕出現指示詞：「這裡」、「這邊」、「看到」、「按」等
* 段落切換的開頭（主題轉換點）
* 講者強調具體數字或列舉（暗示畫面有圖表）

相鄰兩個截圖點至少間隔 20 秒，避免產生過多重複畫面。

### 截圖去重：感知雜湊

擷取完截圖後，`czkawka` 會用感知雜湊（perceptual hashing）比對所有圖片：

```bash
czkawka_cli image \
  --directories "screenshots/<BASENAME>" \
  --max-difference 30 \
  --delete-method "AEB"   # All Except Biggest
```

`--max-difference 30` 是積極模式，特別適合教學影片中大量重複的投影片背景。同群組中保留最大的那張（通常資訊量最豐富），刪除其餘。

### 多影片並行處理

一次輸入多個 URL 時，系統分兩個階段並行執行：

**Phase 1（I/O 密集，bash 並行）**：所有影片同時下載、轉 MP3、呼叫 Groq API。

```bash
process_url_io "$URL_1" 1 &
process_url_io "$URL_2" 2 &
wait
```

**Phase 2（LLM 密集，最多 5 個子 Agent 同時執行）**：每個影片各啟動一個獨立的 `general-purpose` subagent，同步執行逐字稿整理、摘要、截圖、Obsidian 筆記的生成。

第一批（5 個）→ 全部完成 → 第二批（剩餘）→ 完成

這種設計讓 5 個影片的處理時間近似於 1 個影片，而非 5 倍。

---

## Obsidian 筆記結構

最終產出的 Obsidian 筆記結構如下：

```markdown
---
tags: [video-notes]
source: <YouTube URL>
date: YYYY-MM-DD
---
# 影片標題

> 一句話總結這部影片的核心內容

---
## 重點摘要
- 重點一
- 重點二
...

---
## 完整逐字稿（含截圖）

[00:00:00] 開場白...

![[screenshots/<BASENAME>/<BASENAME>_02m30s_操作示範.jpg]]

[00:02:30] 接著講師示範...
```

截圖用 Obsidian 的 wiki-link 語法嵌入（`![[...]]`），圖文依時間順序緊密對應。

---

## 小結

這個 skill 把「看影片 → 整理筆記」這個過程完全自動化。它的設計重點在於：

* **忠實保留原文**：逐字稿不改寫，只組裝
* **視覺資訊不遺漏**：自動分析截圖時機，去除重複畫面
* **可直接使用**：產出的 Obsidian 筆記開箱即用，不需二次編輯
* **批次效率**：多影片並行處理，時間成本近乎恆定

如果你常用 YouTube 學習，這個工具可以大幅降低筆記整理的摩擦力。

---

## 心得

這個 skill 是我第一個認真撰寫的 skill。他的確解決了我日常生活中所遇到的問題。

有一個完整的筆記心得是非常實用的，甚至以後我還可以把它拿給 AI 當作參考的資料，做進一步的應用。而這一切都只需要一個簡單的動作，就可以得到我要的成果。

所以又回到最上面我一開始提的，我認為對開發者來說，有提供 CLI 界面的應用跟 AI 並行之後，將會產生極大的威力。
