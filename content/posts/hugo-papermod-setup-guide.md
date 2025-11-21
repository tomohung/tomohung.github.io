---
date: '2025-11-14T11:56:03+08:00'
draft: false
title: 'Hugo 靜態網站架設教學：從零開始打造你的 Blog'
tags: ["Web", "教學"]
---

# Hugo + PaperMod 主題快速安裝指南

本文將介紹如何使用 Hugo 建立靜態網站，並安裝 PaperMod 主題。

## 前置需求安裝

### 安裝 Hugo

使用 Homebrew 安裝 Hugo：

```bash
brew install hugo
```

### 安裝 Go

使用 asdf 版本管理工具安裝 Go：

```bash
# 添加 Go 插件
asdf plugin add golang https://github.com/asdf-community/asdf-golang.git

# 安裝最新版本的 Go
asdf install golang latest
```

### 安裝 Dart Sass

Hugo 的某些主題需要 Dart Sass 來編譯樣式：

```bash
brew install sass/sass/sass
```

## 建立 Hugo 網站

### 初始化專案

建立一個新的 Hugo 網站（使用 YAML 格式的設定檔）：

```bash
hugo new site MyFreshWebsite --format yaml
```

> 💡 請將 `MyFreshWebsite` 替換成你想要的網站名稱

### 安裝 PaperMod 主題

將 PaperMod 主題作為 Git submodule 加入專案：

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### 設定主題

在 `hugo.yml` 檔案中加入以下設定：

```yaml
theme: ["PaperMod"]
```

## 開始創作

建立你的第一篇文章：

```bash
hugo new content content/posts/my-first-post.md
```

## 參考資源

- [Hugo 官方快速開始指南](https://gohugo.io/getting-started/quick-start)
- [PaperMod 主題安裝文件](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)

---

安裝完成後，你就可以開始使用 `hugo server -D` 啟動本地開發伺服器，預覽你的網站了！

## Update

### 2025-11-22

如果是第一次 git clone 這個 repo 到新電腦上，需要把 PaperMod 的 submodule 也下載下來
```
git submodule init && git submodule update
```

Ref: https://stackoverflow.com/a/1030263
