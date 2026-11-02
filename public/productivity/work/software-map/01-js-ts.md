2026 10 08 Created
2026 11 02 Moved from Discord-peko
---

# JavaScript / TypeScript

## 基本介紹

### JavaScript（JS）

JavaScript：1995 年誕生的程式語言，最初用來讓網頁產生互動，現在也能開發網站後端、桌面與手機 App。

- JavaScript（程式語言）：讓網頁能回應使用者操作，例如按鈕、表單、動態更新內容。
- Node.js（執行環境）：讓 JavaScript 不只在瀏覽器，也能在伺服器上執行。

網頁三大基礎技術：
- HTML：結構（骨架）。
- CSS：外觀（衣服）。
- JavaScript：行為（互動）。

應用範例：
- Frontend：網站互動、React、Vue。
- Backend：使用 Node.js 開發 Web API。
- Desktop：使用 Electron 開發桌面軟體。
- Mobile：使用 React Native 開發手機 App。

### TypeScript（TS）

TypeScript：Microsoft 於 2012 年推出，在 JavaScript 的基礎上加入型別檢查，幫助開發者提早發現程式錯誤。

- TypeScript（程式語言）：可以替變數、函式參數指定資料型別，例如數字、文字。
- 主要特色：在執行前檢查型別錯誤，降低修改程式時不小心用錯資料的機會。
- 執行方式：一般先轉換成 JavaScript，再由瀏覽器或 Node.js 執行。

範例：

```javascript
// JavaScript：沒有指定參數型別
function add(a, b) {
  return a + b;
}
```

```typescript
// TypeScript：指定參數與回傳值都是數字
function add(a: number, b: number): number {
  return a + b;
}
```

差別：
- JS：可以傳入數字，也可以傳入文字；實際結果依資料型別而定。
- TS：如果傳入文字，型別檢查就會提示錯誤。

## 核心特色

### 1. 瀏覽器原生支援
- JavaScript 可直接在瀏覽器執行。
- 不需要另外安裝執行環境，即可建立網頁互動。
- 是 Web 前端的核心程式語言。

### 2. 前後端皆可開發
- Frontend：Browser。
- Backend：Node.js。
- 可使用 JS / TS 開發完整的 Web 應用程式。

### 3. 生態系龐大
- 擁有大量 Library（函式庫）與 Framework（框架）。
- 涵蓋網站、Web API、桌面、行動應用程式等領域。

### 4. TypeScript 型別系統
- JavaScript：動態型別，使用彈性較高。
- TypeScript：增加靜態型別檢查，能提早發現部分錯誤。
- 中大型專案常使用 TypeScript；簡單腳本可直接使用 JavaScript。

## 常見開發工具

| 工具 | 用途 |
|---|---|
| Node.js | JavaScript 執行環境 |
| npm | 套件安裝與依賴管理 |
| VS Code | 程式碼編輯器 |
| TypeScript Compiler（tsc） | 型別檢查、編譯 TypeScript |

補充：
- Node.js：讓 JavaScript 能在瀏覽器外執行。
- npm：通常隨 Node.js 一起安裝。
- TypeScript：可透過 npm 安裝。
- 部分執行環境支援直接執行 TypeScript，但支援範圍與方式有所不同。

## 應用領域

```text
JavaScript / TypeScript
│
├── 🌐 Web ⭐
│   │
│   ├── Frontend（前端）⭐
│   │   ├── React（UI Library）
│   │   ├── Vue（Frontend Framework）
│   │   └── Angular（Frontend Framework）
│   │
│   └── Backend（後端）
│       └── Node.js（JavaScript Runtime）
│           ├── Express（Backend Framework）
│           └── NestJS（Backend Framework）
│
├── 📱 Mobile（行動應用程式）
│   └── React Native
│       → iOS / Android
│
├── 🖥️ Desktop（桌面應用程式）
│   ├── Electron
│   └── Tauri（前端可使用 JS / TS）
│       → Windows / macOS / Linux
│
└── 🛠️ CLI / Automation
    └── Node.js
        → 命令列工具、自動化腳本
```

## 重點整理

- JavaScript：Web 前端核心語言，也能用於後端。
- TypeScript：JavaScript + 靜態型別系統。
- Node.js：執行環境，不是程式語言或框架。
- React / Vue / Angular：常見前端技術。
- Express / NestJS：Node.js 後端框架。
- JS / TS 可跨 Web、Mobile、Desktop 與 CLI 領域。
