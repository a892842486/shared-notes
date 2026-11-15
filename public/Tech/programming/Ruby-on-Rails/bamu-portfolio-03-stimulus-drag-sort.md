2026 09 12 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails Stimulus 技能拖曳排序

## 目的

在後台 Skill 列表加入拖曳排序功能，管理員可以直接拖動技能調整顯示順序，並將結果儲存至資料庫。

使用技術：

- HTML Drag and Drop API
- Stimulus
- Fetch API
- Rails Routes / Controller
- Active Record Transaction

## 1. 建立 Feature Branch

```bash
git switch -c feature/skill-drag-sort
```

## 2. Routes：建立排序 API

`config/routes.rb`

在既有 Admin Namespace 中修改 Skills Routes：

```ruby
namespace :admin do
  resources :skills do
    patch :reorder, on: :collection
  end
end
```

### Collection Route

```ruby
patch :reorder, on: :collection
```

建立：

```text
PATCH /admin/skills/reorder
```

對應：

```text
Admin::SkillsController#reorder
```

- `collection`：針對整個 Skills 集合操作，不需要單筆 Skill ID。
- `PATCH`：更新既有技能的排序資料。

## 3. Controller：更新排序

`app/controllers/admin/skills_controller.rb`

```ruby
def reorder
  skill_ids = params[:skill_ids]

  Skill.transaction do
    skill_ids.each_with_index do |skill_id, index|
      skill = Skill.find(skill_id)
      skill.update!(position: index + 1)
    end
  end
end
```

### 程式邏輯

前端送出：

```json
{
  "skill_ids": ["3", "1", "2"]
}
```

Controller 依照陣列順序更新：

| Skill ID | position |
|---|---|
| 3 | 1 |
| 1 | 2 |
| 2 | 3 |

### each_with_index

```ruby
skill_ids.each_with_index do |skill_id, index|
```

取得每個 Skill ID 與其陣列索引。

由於索引從 `0` 開始，因此使用：

```ruby
skill.update!(position: index + 1)
```

讓排序從 `1` 開始。

### Active Record Transaction

```ruby
Skill.transaction do
  # 更新所有 Skill 的 position
end
```

將多筆更新包在同一個 Transaction 中。

如果其中一筆 `update!` 失敗並拋出例外，整個 Transaction 會 Rollback，避免只更新部分排序。

> 這裡的 Transaction 保證資料庫更新的原子性，但不會自動處理多位管理員同時排序的衝突。

## 4. View：掛載 Stimulus

`app/views/admin/skills/index.html.erb`

```erb
<h1>Skills</h1>

<%= link_to "New Skill", new_admin_skill_path %>

<div id="skills" data-controller="skill-sort">
  <% @skills.each do |skill| %>
    <div class="flex items-center gap-2">
      <p data-skill-id="<%= skill.id %>"
         draggable="true"
         data-action="dragstart->skill-sort#dragstart
                      dragover->skill-sort#dragover
                      drop->skill-sort#drop">
        <%= skill.name %>
      </p>

      <%= button_to "Delete",
                    admin_skill_path(skill),
                    method: :delete %>
    </div>
  <% end %>
</div>
```

### HTML Drag and Drop

| 屬性 | 用途 |
|---|---|
| `draggable="true"` | 允許拖曳元素 |
| `data-skill-id` | 儲存 Skill ID |
| `dragstart` | 開始拖曳 |
| `dragover` | 拖曳經過目標 |
| `drop` | 放開滑鼠，執行排序 |

### Stimulus 掛載

```erb
<div data-controller="skill-sort">
```

連接：

`app/javascript/controllers/skill_sort_controller.js`

Stimulus 使用命名慣例：

```text
skill_sort_controller.js
         ↓
data-controller="skill-sort"
```

## 5. Stimulus Controller：拖曳與排序

建立檔案：

```bash
touch app/javascript/controllers/skill_sort_controller.js
```

`app/javascript/controllers/skill_sort_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {

  dragstart(event) {
    this.draggedSkillId = event.currentTarget.dataset.skillId
  }

  dragover(event) {
    event.preventDefault()
  }

  drop(event) {
    event.preventDefault()

    const draggedSkill = this.element.querySelector(
      `[data-skill-id="${this.draggedSkillId}"]`
    )

    if (!draggedSkill) return

    const draggedRow = draggedSkill.parentElement
    const targetRow = event.currentTarget.parentElement

    const rows = [...this.element.children]

    const draggedIndex = rows.indexOf(draggedRow)
    const targetIndex = rows.indexOf(targetRow)

    if (draggedIndex < targetIndex) {
      targetRow.after(draggedRow)
    } else {
      targetRow.before(draggedRow)
    }

    const skillIds = [...this.element.querySelectorAll("[data-skill-id]")]
      .map(skill => skill.dataset.skillId)

    fetch("/admin/skills/reorder", {
      method: "PATCH",
      headers: {
        "X-CSRF-Token": document.querySelector('meta[name="csrf-token"]').content,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ skill_ids: skillIds })
    })
  }
}
```

### dragstart：記住拖曳對象

```javascript
dragstart(event) {
  this.draggedSkillId = event.currentTarget.dataset.skillId
}
```

取得被拖曳的 Skill ID，儲存在 Stimulus Controller 中。

### dragover：允許放置

```javascript
dragover(event) {
  event.preventDefault()
}
```

取消瀏覽器預設行為，讓目標元素可以接收 `drop` 事件。

### drop：調整畫面順序

```javascript
const draggedRow = draggedSkill.parentElement
const targetRow = event.currentTarget.parentElement
```

找到被拖曳的整列，以及放置位置的整列。

```javascript
if (draggedIndex < targetIndex) {
  targetRow.after(draggedRow)
} else {
  targetRow.before(draggedRow)
}
```

- 往下拖曳：放在目標列後面。
- 往上拖曳：放在目標列前面。

這裡移動的是整個 Row，而不只是技能名稱，因此 Delete 按鈕也會一起移動。

### 取得排序結果

```javascript
const skillIds = [...this.element.querySelectorAll("[data-skill-id]")]
  .map(skill => skill.dataset.skillId)
```

依照目前 DOM 順序，取得所有 Skill ID。

例如：

```javascript
["3", "1", "2"]
```

### Fetch API：傳送排序結果

```javascript
fetch("/admin/skills/reorder", {
  method: "PATCH",
  headers: {
    "X-CSRF-Token": document.querySelector('meta[name="csrf-token"]').content,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ skill_ids: skillIds })
})
```

### 重要參數

| 設定 | 用途 |
|---|---|
| `method: "PATCH"` | 呼叫 Rails 更新路由 |
| `X-CSRF-Token` | 提供 Rails CSRF 驗證 Token |
| `Content-Type` | 告訴 Rails 傳送的是 JSON |
| `JSON.stringify` | 將 JavaScript Object 轉換成 JSON |

Rails Controller 透過：

```ruby
params[:skill_ids]
```

取得前端送出的排序陣列。

## 6. 完整執行流程

```text
管理員拖曳 Skill
       ↓
dragstart
記住 Skill ID
       ↓
dragover
允許放置
       ↓
drop
移動 HTML Row
       ↓
取得所有 Skill ID 順序
       ↓
Fetch API
PATCH /admin/skills/reorder
       ↓
Admin::SkillsController#reorder
       ↓
Skill.transaction
       ↓
更新每筆 Skill.position
       ↓
PostgreSQL 儲存排序
```

### 功能驗證

1. 進入後台 Skills 列表。
2. 拖曳 Skill，確認畫面順序改變。
3. 重新整理頁面，確認排序仍然存在。
4. 檢查資料庫中的 `position` 是否正確。

> 要讓重新整理後的順序維持一致，Controller 的 `index` 必須依照 `position` 排序，例如 `Skill.order(:position)`。這部分未出現在原始頻道紀錄中，需確認專案是否已有設定。

### 原始實作的限制

目前前端在呼叫 `fetch` 後，沒有檢查 HTTP 回應是否成功。

因此，如果伺服器更新失敗，畫面順序仍可能已改變，但資料庫沒有成功儲存。

這是日後可以改善的地方，本篇保留原始實作。

## 7. Git Workflow

```bash
git add .
git commit -m "feat: add skill drag and drop sorting"
```

推送 Feature Branch：

```bash
git push -u origin feature/skill-drag-sort
```

合併至 Main：

```bash
git switch main
git merge feature/skill-drag-sort
git push origin main
```

---

## Git Workflow 摘要

**Feature：** Skill Drag and Sort

**目標：** 使用 Stimulus 實作後台技能拖曳排序，並將結果儲存至資料庫。

### 開發內容

- 建立 Skills Collection Route
- 實作 `reorder` Controller Action
- 使用 Transaction 更新排序
- 掛載 Stimulus Controller
- 使用 HTML Drag and Drop API
- 使用 Fetch API 傳送排序結果
- 將 Skill ID 順序轉換成 `position`

### Branch

`feature/skill-drag-sort`

### Commit History

```text
feat: add skill drag and drop sorting
```
