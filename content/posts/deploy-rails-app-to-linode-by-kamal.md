---
date: '2026-01-12T19:45:56+08:00'
title: '用 Kamal 把 Rails 應用部署到 Linode'
tags: ["Web"]
---

今天下午花了點時間怎麼部署自己在本地端用了一陣子的 Rails 應用，原本考慮用 Railway 這類對入門者友善的平台，但想了想，這種「方便」的方案往往之後會變得麻煩——綁定太深、客製化困難、價格不透明。既然如此，乾脆趁機試試 Rails 官方推薦的 Kamal，搭配 Linode 來解決。

---

## Linode 主機設定

首先在 Linode 建立一台新的主機，拿到 IP 後，把 SSH key 複製過去：

```bash
ssh-copy-id root@Linode_IP
```

之後操作就不用一直輸入密碼。

## Rails 專案設定

根據 [Rails 官方指南](https://guides.rubyonrails.org/getting_started.html#deploying-to-production)，更新 `config/deploy.yml` 設定檔。

## Docker Hub

Kamal 需要從 Docker registry 拉取 image，所以要：

1. 建立 Docker Hub 帳號
2. 產生一個 access token

## 本地環境

記得安裝 Docker for Mac，沒裝的話會沒辦法 build image。

## Kamal 部署

在本地終端機先設定環境變數：

```bash
export KAMAL_REGISTRY_PASSWORD=your-docker-hub-access-token
```

然後執行：

```bash
bin/kamal setup
```

新的 Linode 主機上沒有 Docker，Kamal 會自動安裝，並上傳應用程式。

有一點要注意：Rails 預設要求 HTTPS，所以沒辦法直接用 IP 訪問。

## Cloudflare DNS 設定

我的 DNS 服務是用 Cloudflare。新增一筆 A record 指向 Linode IP，例如 `my-app.domain.com`。

透過 Cloudflare 的 proxy，就能用 HTTPS 連到新網站了。

---

## SQLite 資料庫備份

既然用 SQLite，備份就得自己處理。SSH 進主機後設定 cron job：

```bash
ssh root@你的Linode_IP

# 建立備份資料夾
mkdir -p ~/backups

# 編輯 crontab
crontab -e
```

加入以下排程：

```bash
# 每天凌晨 3 點備份
0 3 * * * cp /var/lib/docker/volumes/my-rails-app_db/_data/production.sqlite3 ~/backups/production_$(date +\%Y\%m\%d).sqlite3

# 每天凌晨 4 點清理 30 天前的備份
0 4 * * * find ~/backups -name "*.sqlite3" -mtime +30 -delete
```

---

## Kamal 常用指令

Kamal 在主機上多疊了一層 Docker，要進入容器操作時可以用：

```bash
bin/kamal console  # 進入 Rails console
bin/kamal shell    # 進入容器 shell
bin/kamal logs     # 查看 logs
```

---

整體來說，Kamal 的部署體驗比想像中順暢。雖然前置設定比 PaaS 多一些，但換來的是完整的控制權和透明的成本結構。也慶幸過去累積了一些背景知識，加上 AI 輔助除錯，大大縮短了排除問題的時間。

