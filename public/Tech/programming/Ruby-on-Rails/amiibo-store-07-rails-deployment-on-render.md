2026 04 24 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 部署至 Render（PostgreSQL）

## 目的

將 Rails 專案部署至 Render，使用 PostgreSQL 作為正式環境資料庫，完成從本地開發到雲端上線的流程。

本篇整合 Amiibo Store 與 Bamu Portfolio 的部署紀錄。

## 1. 部署流程

```text
[1] 本地專案準備
     ↓
[2] 推送 GitHub
     ↓
[3] 建立 Render PostgreSQL (資料庫)
     ↓
[4] 建立 Render Web Service (伺服器)
     ↓
[5] 設定 Build / Start Command
     ↓
[6] 設定 Environment Variables (環境變數)
     ↓
[7] Deploy (部署)
     ↓
[8] 驗證網站
```

## 2. 本地專案準備

確認 Rails 專案已設定使用 PostgreSQL。

`Gemfile`

```ruby
gem "pg"
```

安裝依賴：

```bash
bundle install
```

確認 `config/database.yml` 的正式環境設定能讀取 Render 提供的資料庫連線資訊。

範例：

```yaml
production:
  <<: *default
  url: <%= ENV["DATABASE_URL"] %>
```

> 此範例假設 `database.yml` 已有正確的 `default` 設定。若專案使用 Rails 8 預設 SQLite 或多資料庫設定，需要先調整資料庫配置。

將專案 Commit 並推送至 GitHub：

```bash
git add .
git commit -m "chore(db): setup PostgreSQL"
git push origin main
```

> Commit 訊息應對應實際修改內容；如果 PostgreSQL 已經設定完成，就不需要重複提交。

## 3. 建立 Render PostgreSQL

進入 [Render Dashboard](https://dashboard.render.com/)。

操作：

1. New → PostgreSQL
2. 填寫資料庫名稱
3. 選擇 Region (地區)
4. 選擇適合的方案 (免費)
5. Create Database

原始 Amiibo Store 設定範例：

| 欄位 | 設定 |
|---|---|
| Name | `jdstore-db` |
| Database | `jdstore_production` |
| User | 使用預設產生的帳號 |
| Region | Singapore |

**注意：PostgreSQL 與 Web Service 建議選擇相同 Region。**

建立完成後，在資料庫連線資訊中找到：

`Internal Database URL`

這是提供 Render 內部服務使用的資料庫連線網址。

## 4. 建立 Render Web Service

操作：

1. New → Web Service
2. 選擇 GitHub Repository
3. 設定服務名稱
4. Language 選擇 Ruby
5. Branch 選擇 `main`
6. Region 選擇與 PostgreSQL 相同的地區
7. 選擇方案

如果使用 Public Git Repository，可以從 GitHub 專案頁面：

`Code → HTTPS → 複製 Repository URL`

再貼到 Render。

## 5. 設定 Build / Start Command

### Build Command

```bash
bundle install && bundle exec rails db:prepare && bundle exec rails assets:precompile
```

| 指令 | 用途 |
|---|---|
| `bundle install` | 安裝 Ruby Gems |
| `rails db:prepare` | 準備資料庫並執行必要 Migration |
| `rails assets:precompile` | 編譯 CSS、JavaScript 等靜態資產 |

> 此為整合後的簡化範例。實際部署時，資料庫 Migration 也可以透過 Render 的 Pre-Deploy Command 執行，避免 Build 階段依賴正式資料庫。若專案使用 Yarn 或其他前端建置工具，還需要加入對應安裝與建置指令。

### Start Command

```bash
bundle exec rails server -b 0.0.0.0 -p $PORT
```

- `0.0.0.0`：允許 Render 從容器外部連線。
- `$PORT`：使用 Render 提供的連接埠。

> Start Command 應以專案實際使用的應用伺服器與 Render 設定為準。

## 6. 設定 Environment Variables

進入：

`Render Web Service → Environment`

新增以下環境變數。

### Rails 核心設定

| Key | Value / 用途 |
|---|---|
| `RAILS_ENV` | `production`，使用正式環境 |
| `RAILS_MASTER_KEY` | 本地 `config/master.key` 的內容 |
| `DATABASE_URL` | PostgreSQL 的 Internal Database URL |

### 其他常用設定

| Key | Value / 用途 |
|---|---|
| `RAILS_SERVE_STATIC_FILES` | `true`，讓 Rails 提供靜態資產 |
| `RAILS_LOG_TO_STDOUT` | `true`，將 Log 輸出至標準輸出（需專案支援） |
| `RAILS_MAX_THREADS` | `5`，Puma 執行緒設定（需搭配專案設定） |
| `APP_HOST` | 正式網站網域，供寄信連結等功能使用（需專案有讀取此變數） |
| `BUNDLE_WITHOUT` | `development:test`，略過非正式環境 Gems |

### SECRET_KEY_BASE 與 RAILS_MASTER_KEY

兩者用途不同：

- `RAILS_MASTER_KEY`：解密 Rails Credentials。
- `SECRET_KEY_BASE`：Rails 用於簽署與加密相關資料的核心金鑰。

如果專案已透過 Credentials 提供正式環境所需金鑰，通常不需要額外手動設定 `SECRET_KEY_BASE`。

若專案確實需要獨立設定，可以在本機產生：

```bash
bin/rails secret
```

將產生的值設定為 Render 的 `SECRET_KEY_BASE`。

### 金鑰安全

- `RAILS_MASTER_KEY` 不可提交至公開 GitHub。
- `DATABASE_URL` 包含資料庫帳號與密碼，不可公開。
- 環境變數不要直接寫入程式碼。
- 複製金鑰時，確認沒有多餘的空白或字元。

### 效能設定

原始紀錄曾使用：

```text
RAILS_MAX_THREADS=5
WEB_CONCURRENCY=2
```

概念上，兩個 Worker、每個 Worker 五條執行緒，最多可提供十條應用程式執行緒處理請求。

但實際並行能力仍受資料庫連線池、CPU、記憶體及其他限制影響。

**小型免費服務不建議直接強制設定 `WEB_CONCURRENCY=2`。** 可以先沿用 Render 或專案預設值，再依照記憶體使用情況調整。

## 7. Deploy 與驗證

儲存設定後執行 Deploy。

確認 Render Logs 顯示服務正常啟動。

驗證項目：

- 首頁可以正常開啟。
- CSS、JavaScript、圖片正常載入。
- PostgreSQL 連線成功。
- 資料表已建立並完成 Migration。
- Devise 登入功能正常。
- 後台管理頁面可以操作。

若部署失敗，優先檢查：

1. `DATABASE_URL` 是否正確。
2. `RAILS_MASTER_KEY` 是否正確。
3. PostgreSQL 與 Web Service 是否位於相同 Region。
4. `database.yml` 是否正確設定。
5. Migration 或 Assets 編譯是否失敗。
6. Render Logs 顯示的實際錯誤。

---

## 8. 補充：Active Storage + S3

如果 Rails 專案使用 Active Storage，並將圖片儲存在 S3 相容的物件儲存服務，可能需要額外設定：

```text
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=...
S3_BUCKET=...
```

這些變數需要搭配 `config/storage.yml` 與實際儲存服務設定。

> 不同 S3 相容服務的 Endpoint、Region 與認證方式可能不同，不能只新增環境變數就視為完成圖片上傳設定。

---

## Git Workflow

**Feature：** Project Deployment（Render）

**目標：** 將 Rails 專案部署至 Render，完成 PostgreSQL、環境變數、Web Service 與正式環境驗證。

### 開發內容

- 設定 PostgreSQL
- 推送專案至 GitHub
- 建立 Render PostgreSQL
- 建立 Render Web Service
- 設定 Build / Start Command
- 設定 Rails Environment Variables
- 部署並驗證正式網站

### Commit History

原始紀錄中明確提供的 Commit：

```text
chore(db): setup PostgreSQL
```

其餘部署設定主要透過 Render Dashboard 完成，不一定會產生 Git Commit。
