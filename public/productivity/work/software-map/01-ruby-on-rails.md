2026 10 07 Created
2026 11 02 Moved from Discord-peko
---

# Ruby on Rails

## 基本介紹

Ruby on Rails（Rails）：使用 Ruby 開發的 Web Framework（網頁框架），提供網站開發所需的架構與常用功能，強調快速開發。

- Ruby（程式語言）：語法簡潔、容易閱讀，重視開發者體驗。
- Rails（網頁框架）：內建路由、資料庫操作、表單處理等功能。

應用範例：
- Ruby：自動化腳本、小工具、資料處理。
- Rails：會員系統、電商、部落格、Web API。

## 核心特色

### Convention over Configuration（慣例優於設定）
- Rails 提供預設規則與架構。
- 遵循慣例即可減少設定，加快開發速度。

### DRY（Don't Repeat Yourself）
- 避免重複撰寫相同知識與程式邏輯。
- 透過共用程式碼降低維護成本。

### 主要優勢
- 開發效率高。
- 適合快速迭代產品。
- 能快速建立完整的 Web 應用程式。

知名使用案例： GitHub、Shopify、Airbnb（早期）、Twitch（早期）。

## 常見開發工具

| 工具 | 用途 |
|---|---|
| Ruby | 程式語言 |
| Rails | Web 開發框架 |
| RubyGems | Ruby 套件管理系統 |
| Bundler | 管理專案 Gem 與版本 |
| Git | 版本控制、分支管理 |
| PostgreSQL | 關聯式資料庫 |
| VS Code | 程式碼編輯器 |
| RubyMine | Ruby / Rails 專用 IDE |
| Linux | 常見伺服器作業系統 |

補充：
- Ruby 通常透過版本管理工具（如 rbenv、mise）安裝及管理。
- Rails 通常透過 RubyGems 安裝。
- Bundler 根據 `Gemfile`、`Gemfile.lock` 管理專案依賴。

## 應用領域與技術架構

```text
Ruby on Rails
│
└── 🌐 Web 應用程式
    │
    ├── Backend（後端）⭐
    │   ├── Ruby（程式語言）
    │   └── Rails（Web Framework）
    │
    ├── Frontend（前端）
    │   ├── HTML / ERB → 網頁結構、動態模板
    │   ├── CSS → 樣式、排版
    │   ├── JavaScript → 互動功能
    │   ├── Stimulus → JavaScript 互動控制
    │   └── Tailwind CSS → CSS 工具框架
    │
    ├── Database（資料庫）
    │   ├── SQL → 資料庫查詢語言
    │   └── PostgreSQL → 關聯式資料庫
    │
    ├── 開發工具 / 環境
    │   ├── Git → 版本控制
    │   └── Linux → 常見伺服器環境
    │
    └── 常見產品類型
        ├── E-commerce → 電商
        ├── SaaS → 軟體即服務
        ├── Startup Products → 新創產品
        └── MVP → 最小可行產品
```

## 常見前端搭配

```text
Rails（Backend / API）
│
├── React（UI Library）
├── Vue（Frontend Framework）
└── Angular（Frontend Framework）
    │
    └── JavaScript / TypeScript
```

- React、Vue、Angular 都以 JavaScript / TypeScript 生態系為主。
- 可搭配 Rails API，實現前後端分離。
- 常用於 SPA（Single Page Application／單頁應用程式）。
- SPA：透過局部更新畫面，減少整頁重新載入。

補充： Rails 不一定需要搭配 React / Vue / Angular，也可以使用 ERB + Hotwire（Turbo / Stimulus）建立完整且具有互動性的網站。