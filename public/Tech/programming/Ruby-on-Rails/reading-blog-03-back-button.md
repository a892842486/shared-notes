2026 10 05 Created
2026 11 17 Moved from peko-discord
---

# macOS Ruby on Rails Stimulus 返回上一頁按鈕

## 目的

在 Portfolio 與 Reading Blog 的指定頁面加入返回上一頁的左箭頭按鈕，並透過共用 Partial 避免重複撰寫程式碼。

使用技術：

- Stimulus：處理按鈕點擊事件。
- JavaScript History API：返回瀏覽器上一頁。
- Rails Partial：共用按鈕元件。
- Tailwind CSS：設定按鈕樣式與位置。

## 1. 建立 Stimulus Controller

```bash
touch app/javascript/controllers/back_button_controller.js
```

`app/javascript/controllers/back_button_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  goBack() {
    window.history.back()
  }
}
```

### window.history.back()

呼叫瀏覽器的 History API，返回瀏覽紀錄中的上一頁。

相當於使用者按下瀏覽器的返回按鈕。

> 這不是固定導向某個 Rails Route。如果使用者直接開啟頁面，瀏覽器沒有上一頁紀錄，按鈕可能不會產生預期的導覽效果。

## 2. 建立共用 Partial

```bash
touch app/views/shared/_back_button.html.erb
```

`app/views/shared/_back_button.html.erb`

```erb
<button
  type="button"
  data-controller="back-button"
  data-action="click->back-button#goBack"
  class="fixed left-6 top-10 z-50 flex h-14 w-14 items-center justify-center
         rounded-xl bg-[#4F4B43]/70 text-3xl text-[#F5F1E8] shadow-md
         transition hover:bg-[#4F4B43]
         md:left-10 md:top-30"
  aria-label="Back"
>
  ←
</button>
```

### 核心設定

| 程式碼 | 用途 |
|---|---|
| `data-controller="back-button"` | 掛載 Stimulus Controller |
| `data-action="click->back-button#goBack"` | 點擊時執行 `goBack()` |
| `fixed left-6 top-10` | 固定在畫面左上方 |
| `rounded-xl` | 按鈕圓角 |
| `hover:bg-[#4F4B43]` | 滑鼠懸停時改變背景 |
| `aria-label="Back"` | 提供輔助技術辨識的按鈕名稱 |

## 3. 在指定頁面掛載

將以下程式碼放在 View 最上方：

```erb
<%= render "shared/back_button" %>
```

使用位置：

```text
app/views/admin/projects/_form.html.erb
app/views/projects/show.html.erb
app/views/admin/readings/_form.html.erb
app/views/readings/show.html.erb
```

四個頁面共用同一個 Partial，未來修改按鈕外觀或行為時，只需要維護一份程式碼。

## 4. Git Workflow

```bash
git add .
git commit -m "feat(navigation): add back button"
```

---

## Git Workflow 摘要

**Feature：** Back Button

**目標：** 使用 Stimulus 與 Rails Partial，在指定頁面建立共用返回按鈕。

### 開發內容

- 建立 `back_button_controller.js`
- 使用 `window.history.back()` 返回上一頁
- 建立 `_back_button.html.erb` 共用 Partial
- 在 Portfolio / Reading Blog 四個 View 掛載按鈕

### Commit History

```text
feat(navigation): add back button
```
