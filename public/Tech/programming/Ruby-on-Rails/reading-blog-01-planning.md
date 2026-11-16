2026 10 02 Created
2026 11 16 Moved from peko-discord
---

# macOS Ruby on Rails Reading Blog 專案規劃

## 目的

在既有的 Bamu Portfolio（Ruby on Rails）中，新增 Reading Blog 閱讀心得與技術筆記功能。

本篇記錄開發前的需求分析、頁面規劃、資料管理與開發順序。

**專案定位**

- 基於 Bamu Portfolio 擴充，不另外建立 Rails 專案。
- 使用 `feature/reading-blog` 分支開發。
- 沿用既有 Admin 登入、版型與資料庫。
- 完成後整合至 Portfolio，並新增 Navbar 連結。

## 1. 開發前規劃

開發前文字整理：

1. 網站定位與預計功能。
2. 資料庫欄位與關聯（ERD）。
3. 前台 Index / Show 頁面。
4. 後台 Admin CRUD。
5. 舊閱讀筆記匯入與資料備份方式。

## 2. 前台功能規劃

### Reading Index：文章首頁

| 功能 | 預計實作 |
|---|---|
| 標題 | Reading Blog |
| 閱讀理念 | 直接寫在 View |
| 搜尋 | 依文章標題搜尋，原列表顯示篩選結果 |
| 精選推薦 | 手動挑選 3～5 篇文章 |
| 全部文章 | 依年份分組，按閱讀日期排序 |
| 呈現方式 | 簡潔文字列表，方便瀏覽 |

**文章排序規則**

- 優先依 `read_at` 由新到舊排列。
- `read_at` 為空時，使用 `created_at` 作為替代日期。
- 沒有閱讀日期的文章，排在有閱讀日期的文章之後。

### Reading Show：文章內容

| 欄位 | 用途 |
|---|---|
| `title` | 文章標題 |
| `content` | 閱讀心得或技術筆記 |
| `image` | 多張圖片，規劃左右切換 |
| `read_at` | 閱讀日期，可空白 |
| `created_at` | Rails 自動記錄建立時間 |
| `updated_at` | Rails 自動記錄更新時間 |

頁面不顯示更新日期。

### 搜尋與精選推薦

**搜尋**

- 只搜尋 Reading 文章標題。
- 搜尋框放在文章列表上方。
- 不另外建立搜尋結果頁面。

**精選推薦**

- 手動挑選 3～5 篇文章。
- 放在搜尋框下方。
- 點擊標題進入 Reading Show。
- 初期直接在 View 指定，不另外新增資料庫欄位。

## 3. 後台功能規劃

沿用 Bamu Portfolio 既有 Admin 架構。

| 頁面 | 功能 |
|---|---|
| Admin Reading Index | 文章列表、新增、編輯、刪除 |
| Admin Reading New | 新增文章 |
| Admin Reading Edit | 編輯文章、管理圖片 |

目標是讓文章可以透過後台管理，不必每次新增內容都修改程式碼。

## 4. 資料匯入與備份規劃

### 初始資料匯入

- 將精選舊筆記整理成 Markdown 或 JSON。
- 透過 Rails Seeds 匯入資料庫。
- Seeds 只負責初始資料，不作為日常儲存機制。

### 日常資料與備份

- Admin 新增、修改的文章儲存在資料庫。
- 規劃 JSON／Markdown 匯出，方便備份及重新匯入。
- 使用 Active Storage 管理圖片。
- 規劃圖片持久化儲存與備份。
- 不依賴免費 Render 資料庫永久保存資料。

> 以上是開發前的備份需求，不代表所有匯出、還原功能都已完成。

## 5. 前端學習目標

| 技術 | 學習方向 |
|---|---|
| HTML | 語意化結構、頁面布局 |
| CSS / Tailwind | Flexbox、Grid、RWD |
| JavaScript / Stimulus | 圖片切換等互動功能 |
| UI / UX | 文章列表、閱讀頁面設計 |
| RWD | 手機與桌面版閱讀體驗 |

開發方向：先熟悉 HTML、CSS、JavaScript 與 Rails View 整合，暫不導入 React 或 Vue。

## 6. 預定開發順序

1. 建立 `feature/reading-blog` 分支。
2. 建立 Reading Model、資料庫欄位與關聯。
3. 開發公開首頁：搜尋、精選推薦、年份文章列表。
4. 開發 Reading Show 與圖片切換。
5. 開發 Admin Reading CRUD。
6. 整理 Seeds、資料匯出與備份方案。
7. 整合至 Bamu Portfolio，新增 Navbar 連結。

---

## 專案規劃摘要

**Project：** Reading Blog

**基底：** Bamu Portfolio（Ruby on Rails）

**目標：** 在既有 Portfolio 新增閱讀心得與技術筆記部落格，並支援後台文章管理。

**規劃重點：**

- 紙筆需求規劃與資料模型設計
- 前台 Reading Index / Show
- 後台 Reading CRUD
- 標題搜尋與精選推薦
- 多圖片展示
- Seeds 資料匯入
- JSON／Markdown 備份方案
- 前端排版與互動練習

**Branch：** `feature/reading-blog`
