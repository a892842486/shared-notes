2026 10 16 Created
2026 11 17 Moved from peko-discord
---

# macOS Ruby on Rails Stimulus 全站深色模式

## 目的

在 Bamu Portfolio 加入 Light / Dark Mode（網站開關燈），並將功能套用至 Reading Blog、Portfolio 及 Admin 後台。

使用技術：

- CSS Variables：定義明暗主題顏色。
- Stimulus：控制主題切換。
- localStorage：保存使用者選擇。
- Tailwind CSS v4：設定深色模式樣式。

## 1. CSS Variables：建立明暗主題

`app/assets/stylesheets/application.css`

```css
@import url("https://fonts.googleapis.com/css2?family=LINE+Seed+Sans+TC:wght@400;500;600;700&family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap");

:root {
  color-scheme: light;

  --color-page: #F5F1E8;
  --color-surface: #FFFFFF;
  --color-text: #4F4B43;
  --color-muted: #77736A;
  --color-accent: #777F5B;
}

:root.dark {
  color-scheme: dark;

  --color-page: #252622;
  --color-surface: #33342E;
  --color-text: #F0EEE6;
  --color-muted: #C5C1B6;
  --color-accent: #BFCBA0;
}

body {
  font-family: "Plus Jakarta Sans", "LINE Seed Sans TC", sans-serif;
  background-color: var(--color-page);
  color: var(--color-text);
}
```

### 主題色對照

| 變數 | Light | Dark |
|---|---|---|
| `--color-page` | `#F5F1E8` | `#252622` |
| `--color-surface` | `#FFFFFF` | `#33342E` |
| `--color-text` | `#4F4B43` | `#F0EEE6` |
| `--color-muted` | `#77736A` | `#C5C1B6` |
| `--color-accent` | `#777F5B` | `#BFCBA0` |

透過 CSS Variables，可以在切換 `.dark` 時統一替換顏色。

重新編譯 Tailwind：

```bash
bin/rails tailwindcss:build
```

## 2. Stimulus：切換與記住主題

建立 Controller：

```bash
bin/rails generate stimulus Theme
```

`app/javascript/controllers/theme_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  connect() {
    const savedTheme = localStorage.getItem("theme")

    if (savedTheme === "dark") {
      document.documentElement.classList.add("dark")
    }
  }

  toggle() {
    const isDark = document.documentElement.classList.toggle("dark")

    localStorage.setItem("theme", isDark ? "dark" : "light")
  }
}
```

### 方法說明

| 方法 | 用途 |
|---|---|
| `connect()` | 讀取先前儲存的主題 |
| `localStorage.getItem()` | 取得使用者設定 |
| `classList.toggle()` | 切換 `<html>` 的 `.dark` Class |
| `localStorage.setItem()` | 儲存明暗主題選擇 |

### 執行流程

```text
進入網站
   ↓
Stimulus connect()
   ↓
讀取 localStorage
   ↓
套用已儲存的 Dark Mode

使用者點擊開關燈
   ↓
Stimulus toggle()
   ↓
切換 <html> 的 .dark
   ↓
CSS / Tailwind 更新顏色
   ↓
localStorage 保存選擇
```

## 3. Navbar：加入開關燈按鈕

`app/views/shared/_navbar.html.erb`

在 Navbar 掛載 Stimulus：

```erb
<nav data-controller="theme">
```

加入切換按鈕：

```erb
<button
  type="button"
  data-action="click->theme#toggle"
  aria-label="Toggle dark mode"
  title="Toggle dark mode"
>
  <i class="fa-solid fa-moon" aria-hidden="true"></i>
</button>
```

桌面與手機版 Navbar 都加入按鈕，並共用同一個 Stimulus Controller 的 `toggle()`。

> 以上只保留功能核心，原始按鈕的 Tailwind 排版可參考開發紀錄。

## 4. Tailwind v4：設定手動深色模式

`app/assets/tailwind/application.css`

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));
```

### @custom-variant dark

將 Tailwind 的 `dark:` Variant 改為依照 `.dark` Class 判斷。

例如：

```erb
<div class="bg-[#F5F1E8] dark:bg-[#252622]">
```

- 沒有 `.dark`：使用淺色背景。
- 存在 `.dark`：使用深色背景。

這樣網站就由自己的主題切換按鈕控制，不必直接跟隨作業系統的深色模式。

## 5. 全站 Views：加入 Dark Mode

這次修改不只包含 Reading Blog，而是涵蓋整個 Portfolio。

### 修改範圍

| 區域 | 修改內容 |
|---|---|
| Reading Index / Show | 文章列表、搜尋框、卡片、文字、圖片區域 |
| Admin Reading | 列表、表單、編輯、新增、備份及刪除按鈕 |
| Profile / Admin Profile | 個人介紹、後台表單 |
| Project / Admin Project | 作品卡片、詳細頁、後台表單 |
| Link / Admin Link | 連結元件與管理介面 |
| Navbar | 桌面與手機版切換按鈕 |

### Tailwind Dark Variant 範例

文字：

```css
text-[#4F4B43] dark:text-[#F0EEE6]
```

次要文字：

```css
text-[#77736A] dark:text-[#C5C1B6]
```

卡片背景：

```css
bg-[#EEEAE0] dark:bg-[#33342E]
```

卡片 Hover：

```css
hover:bg-[#E8E4DA] dark:hover:bg-[#45473E]
```

輸入欄位：

```css
bg-[#F5F1E8] dark:bg-[#33342E]
border-[#CFC9BB] dark:border-[#55574D]
```

### 開發方式

1. 先定義全站 Light / Dark 主題色。
2. 實作 Stimulus 主題切換。
3. 設定 Tailwind `dark:` Variant。
4. 逐一檢查前台與後台 Views。
5. 補上文字、背景、邊框、Hover 等深色樣式。
6. 確認桌面與手機版顯示正常。

> 原始紀錄只列出部分 View 的修改內容，實際開發範圍更廣。本篇以功能與修改原則為主，不逐一列出所有 Tailwind Class。

## 6. 功能驗證

- 點擊 Navbar 按鈕，確認 Light / Dark 切換。
- 重新整理頁面，確認主題選擇保留。
- 切換前台、後台頁面，確認主題一致。
- 檢查搜尋框、表單、按鈕、卡片的文字與背景對比。
- 檢查桌面與手機版 Navbar。

---

## 開發摘要

**Feature：** Site-wide Dark Mode

**目標：** 在 Bamu Portfolio 加入全站明暗主題切換，並保存使用者選擇。

### 開發內容

- 建立 Light / Dark CSS Variables
- 使用 Stimulus 切換 `.dark`
- 使用 localStorage 保存主題
- 設定 Tailwind v4 `@custom-variant dark`
- 加入桌面與手機版 Navbar 開關
- 調整 Reading、Profile、Project、Link 及 Admin Views
- 統一全站背景、文字、表單、卡片與 Hover 樣式

**技術重點：** CSS Variables + Stimulus + Tailwind Dark Variant
