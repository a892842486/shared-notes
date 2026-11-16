2026 09 16 Created
2026 11 16 Moved from peko-discord
---

# macOS Ruby on Rails Project 技能勾選與自動化測試

## 目的

在 Portfolio 後台建立 Project 技能勾選功能，讓管理員可以指定每個作品使用的 Skills，並透過自動化測試確認資料關聯正確。

開發內容：

1. 建立 Project Skills Checkbox
2. 設定 Strong Parameters，允許儲存多個 Skill ID
3. 建立 Fixtures 測試資料
4. 測試新增 Project 時指定 Skills
5. 測試編輯 Project 時取消 Skills

## 1. 建立 Feature Branch

```bash
git switch -c feature/project-skill-assignment
```

## 2. View：建立技能勾選選單

`app/views/admin/projects/_form.html.erb`

```erb
<div>
  <%= form.label :skills %>
  <%= form.collection_check_boxes :skill_ids, Skill.order(:position), :id, :name %>
</div>
```

### collection_check_boxes

Rails 會從資料庫取得 Skills，自動產生多選 Checkbox。

```erb
<%= form.collection_check_boxes :skill_ids, Skill.order(:position), :id, :name %>
```

| 參數 | 用途 |
|---|---|
| `:skill_ids` | Project 關聯的 Skill ID 陣列 |
| `Skill.order(:position)` | 依照 position 排列 Skills |
| `:id` | Checkbox 提交的值 |
| `:name` | Checkbox 顯示的文字 |

例如：

```text
Project：Amiibo Store

☑ Ruby
☑ Ruby on Rails
☐ Python
☐ JavaScript
```

勾選後，表單會傳送對應的 `skill_ids`。

這個功能使用第一篇建立的多對多關聯：

```ruby
has_many :project_skills
has_many :skills, through: :project_skills
```

## 3. Controller：允許儲存 Skill IDs

`app/controllers/admin/projects_controller.rb`

原本：

```ruby
def project_params
  params.require(:project).permit(:name, :summary, :description)
end
```

修改為：

```ruby
def project_params
  params.require(:project).permit(
    :name,
    :summary,
    :description,
    skill_ids: []
  )
end
```

### Strong Parameters

```ruby
skill_ids: []
```

允許接收多個 Skill ID。

例如：

```ruby
{
  name: "Amiibo Store",
  skill_ids: [1, 2]
}
```

Rails 透過 `skill_ids` 更新 Project 與 Skills 的多對多關聯。

- 新增勾選：建立對應的 `ProjectSkill` 關聯。
- 取消勾選：移除不再指定的關聯。
- 原有勾選：保留關聯。

不需要自行逐筆建立或刪除 `ProjectSkill`。

## 4. 自動化測試

測試目標：

- 建立 Project 時，可以指定多個 Skills。
- 編輯 Project 時，可以取消已指定的 Skill。
- 透過測試確認資料庫中的關聯結果。

### 4-1. Fixtures：建立測試資料

`test/fixtures/skills.yml`

```yaml
one:
  name: Ruby
  position: 1

two:
  name: Ruby on Rails
  position: 2
```

Fixtures 是 Rails 的測試資料。

這裡建立兩筆 Skill，供後續測試使用：

- `skills(:one)`：Ruby
- `skills(:two)`：Ruby on Rails

先執行既有測試：

```bash
bin/rails test test/controllers/admin/projects_controller_test.rb
```

當時結果：

```text
1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

### 4-2. Create Test：建立 Project 並指定 Skills

`test/controllers/admin/projects_controller_test.rb`

```ruby
test "creates project with skills" do
  ruby = skills(:one)
  rails = skills(:two)

  post admin_projects_path, params: {
    project: {
      name: "Test Project",
      summary: "Test summary",
      description: "Test description",
      skill_ids: [ruby.id, rails.id]
    }
  }

  project = Project.find_by(name: "Test Project")

  assert_equal [ruby.id, rails.id].sort, project.skill_ids.sort
end
```

### 測試流程

```text
取得 Fixtures：Ruby、Rails
          ↓
模擬送出新增 Project 表單
          ↓
傳入 skill_ids: [ruby.id, rails.id]
          ↓
從資料庫取得新建立的 Project
          ↓
確認 Project 擁有兩個指定 Skills
```

重點程式碼：

```ruby
post admin_projects_path, params: {
  project: {
    name: "Test Project",
    summary: "Test summary",
    description: "Test description",
    skill_ids: [ruby.id, rails.id]
  }
}
```

`post` 模擬使用者送出新增表單。

```ruby
assert_equal [ruby.id, rails.id].sort, project.skill_ids.sort
```

確認實際儲存的 Skill IDs 與預期一致。

使用 `.sort` 是為了避免陣列順序不同造成測試失敗。

執行：

```bash
bin/rails test test/controllers/admin/projects_controller_test.rb
```

當時結果：

```text
2 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

### 4-3. Update Test：取消已勾選的 Skill

`test/controllers/admin/projects_controller_test.rb`

```ruby
test "updates project skills" do
  ruby = skills(:one)
  rails = skills(:two)

  project = Project.create!(
    name: "Test Project",
    summary: "Test summary",
    description: "Test description",
    skill_ids: [ruby.id, rails.id]
  )

  patch admin_project_path(project), params: {
    project: {
      skill_ids: [ruby.id]
    }
  }

  project.reload

  assert_equal [ruby.id], project.skill_ids
end
```

### 測試流程

```text
建立 Project
Skills：Ruby、Rails
          ↓
模擬編輯 Project
只傳入 Ruby 的 Skill ID
          ↓
Rails 更新 Project Skills
          ↓
project.reload
重新讀取資料庫
          ↓
確認只剩 Ruby
```

重點程式碼：

```ruby
patch admin_project_path(project), params: {
  project: {
    skill_ids: [ruby.id]
  }
}
```

模擬取消 Rails 的勾選，只保留 Ruby。

```ruby
project.reload
```

重新從資料庫載入 Project，避免只檢查記憶體中的舊資料。

```ruby
assert_equal [ruby.id], project.skill_ids
```

確認 Rails 已經從 Project 的關聯中移除。

執行：

```bash
bin/rails test test/controllers/admin/projects_controller_test.rb
```

當時結果：

```text
3 runs, 3 assertions, 0 failures, 0 errors, 0 skips
```

### 4-4. 執行完整測試

```bash
bin/rails test
```

Rails 會執行測試套件中符合預設規則的測試。

用途：後續修改程式時，重新檢查既有功能是否受到影響。

> 自動化測試可以降低修改程式造成 Regression（功能退化）的風險，但只能驗證已經寫入測試的行為。

## 5. Git Workflow

提交變更：

```bash
git add .
git commit -m "feat: add project skill assignment"
```

推送 Feature Branch：

```bash
git push -u origin feature/project-skill-assignment
```

合併至 Main：

```bash
git switch main
git merge feature/project-skill-assignment
git push
```

---

## Git Workflow 摘要

**Feature：** Project Skill Checkbox & Tests

**目標：** 讓管理員透過 Checkbox 指定 Project Skills，並以自動化測試驗證新增與更新關聯。

### 開發內容

- 使用 `collection_check_boxes` 建立技能勾選選單
- 使用 Strong Parameters 允許 `skill_ids` 陣列
- 建立 Skill Fixtures
- 測試建立 Project 並指定多個 Skills
- 測試編輯 Project 並取消指定的 Skill
- 使用 `bin/rails test` 執行自動化測試

### Branch

`feature/project-skill-assignment`

### Commit History

```text
feat: add project skill assignment
```
