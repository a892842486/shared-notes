2026 04 24 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 資料模型設計（Association & Validation）

## 目的

建立 Portfolio 的資料模型，管理個人資訊、技能、作品及相關連結。

透過 Active Record Association 建立資料關聯，並使用 Validation 確保資料符合規則。

## 1. 資料模型架構

| Model | 用途 |
|---|---|
| Profile | 個人資料：姓名、職稱、自介、Email |
| Skill | 技能清單，使用 `position` 排序 |
| Project | 作品名稱、摘要與介紹 |
| ProjectSkill | Project 與 Skill 的關聯表，使用 `position` 排序 |
| Link | GitHub、Demo 等網址，透過多型關聯共用 |

### 欄位設計

```text
Profile
├── display_name    :string
├── job_title       :string
├── introduction    :text
└── contact_email   :string

Skill
├── name            :string
└── position        :integer

Project
├── name            :string
├── summary         :string
└── description     :text

ProjectSkill
├── project         :references
├── skill           :references
└── position        :integer

Link
├── linkable_type   :string
├── linkable_id     :bigint
├── label           :string
├── url             :string
└── position        :integer
```

## 2. 建立 Migration

建立開發分支：

```bash
git checkout -b feature/data-models
```

建立 Model：

```bash
bin/rails generate model Profile
```

修改 Migration：

`db/migrate/<timestamp>_create_profiles.rb`

```ruby
create_table :profiles do |t|
  t.string :display_name
  t.string :job_title
  t.text :introduction
  t.string :contact_email

  t.timestamps
end
```

其餘 Model 依照前面的欄位架構建立。

例如，ProjectSkill 使用 References：

```ruby
t.references :project, null: false, foreign_key: true
t.references :skill, null: false, foreign_key: true
t.integer :position
```

Link 使用 Polymorphic References：

```ruby
t.references :linkable, polymorphic: true, null: false
t.string :label
t.string :url
t.integer :position
```

> 這兩段是依照原始欄位架構補充的 Migration 範例，不是 Discord 紀錄中提供的完整 Migration 原始碼。

執行：

```bash
bin/rails db:migrate
```

**Migration 決定資料長什麼樣子；Association 決定資料彼此怎麼連在一起。**

## 3. 建立 Model Associations

### Profile

`app/models/profile.rb`

```ruby
class Profile < ApplicationRecord
  has_many :links, as: :linkable, dependent: :destroy
end
```

一筆 Profile 可以擁有多筆 Link。

### Skill

`app/models/skill.rb`

```ruby
class Skill < ApplicationRecord
  has_many :project_skills, dependent: :destroy
  has_many :projects, through: :project_skills
end
```

一個 Skill 可以被多個 Project 使用。

### Project

`app/models/project.rb`

```ruby
class Project < ApplicationRecord
  has_many :project_skills, dependent: :destroy
  has_many :skills, through: :project_skills

  has_many :links, as: :linkable, dependent: :destroy
end
```

一個 Project 可以擁有多個 Skill 與 Link。

### ProjectSkill

`app/models/project_skill.rb`

```ruby
class ProjectSkill < ApplicationRecord
  belongs_to :project
  belongs_to :skill
end
```

ProjectSkill 是 Project 與 Skill 之間的中介 Model。

### Link

`app/models/link.rb`

```ruby
class Link < ApplicationRecord
  belongs_to :linkable, polymorphic: true
end
```

Link 可以屬於 Profile，也可以屬於 Project。

## 4. 重要關聯設計

### has_many :through（多對多關聯）

```text
Project
   │
   └── ProjectSkill ── Skill
```

例如：

- Portfolio Website 使用 Ruby on Rails、PostgreSQL。
- Amiibo Store 也使用 Ruby on Rails、PostgreSQL。

因此，一個 Project 可以有多個 Skill，一個 Skill 也能對應多個 Project。

```ruby
has_many :skills, through: :project_skills
```

透過 ProjectSkill 中介表管理關聯，還可以利用 `position` 記錄技能在個別作品中的顯示順序。

### Polymorphic Association（多型關聯）

```text
Profile ──┐
          ├── Link
Project ──┘
```

Profile 與 Project 都需要管理 GitHub、Demo 等連結。

如果分別建立 ProfileLink、ProjectLink，會出現相似的資料結構。

使用 Polymorphic Association，就能共用 Link Model。

```ruby
belongs_to :linkable, polymorphic: true
```

Link 透過兩個欄位辨識所屬資料：

- `linkable_type`：所屬 Model，例如 `Profile`。
- `linkable_id`：所屬資料的 ID。

## 5. Model Validation

Validation 用來限制不合法的資料寫入資料庫。

### Profile

```ruby
validates :display_name, presence: true
validates :job_title, presence: true
validates :introduction, presence: true
validates :contact_email, presence: true
```

個人資料的四個欄位都必須填寫。

### Skill

```ruby
validates :name, presence: true, uniqueness: true
```

技能名稱不可空白，也不可重複。

### Project

```ruby
validates :name, presence: true
validates :summary, presence: true
validates :description, presence: true
```

作品名稱、摘要與介紹都必須填寫。

### ProjectSkill

```ruby
validates :skill_id, uniqueness: { scope: :project_id }
```

限制同一個 Project 不可重複加入相同 Skill。

例如：

```text
Project A + Ruby on Rails → 允許
Project A + PostgreSQL    → 允許
Project A + Ruby on Rails → 不允許重複
Project B + Ruby on Rails → 允許
```

> Validation 是 Rails 應用程式層級的驗證。若要在資料庫層級防止重複，還可以建立 `[:project_id, :skill_id]` 的 Unique Index。

### Link

```ruby
validates :label, presence: true
validates :url, presence: true
```

連結名稱與網址不可空白。

> `presence: true` 只檢查是否有值，不會驗證 URL 格式或 Email 格式。

## 6. Rails Console 測試

開啟：

```bash
bin/rails console
```

### 建立 Profile

```ruby
profile = Profile.create!(
  display_name: "你的名字",
  job_title: "Ruby on Rails Backend Developer",
  introduction: "正在轉職 Ruby on Rails 後端工程師。",
  contact_email: "example@example.com"
)
```

### 建立 Skill

```ruby
rails_skill = Skill.create!(
  name: "Ruby on Rails",
  position: 1
)

postgresql_skill = Skill.create!(
  name: "PostgreSQL",
  position: 2
)
```

### 建立 Project

```ruby
project = Project.create!(
  name: "Portfolio Website",
  summary: "使用 Ruby on Rails 製作的個人作品集網站。",
  description: "用來展示個人介紹、技術能力與開發作品。"
)
```

### 測試多對多關聯

```ruby
project.skills << rails_skill
project.skills << postgresql_skill
```

查詢：

```ruby
project.skills
rails_skill.projects
```

透過 Active Record Association，Rails 會自動建立對應的 ProjectSkill 關聯紀錄。

### 測試多型關聯

建立 Profile Link：

```ruby
profile.links.create!(
  label: "GitHub",
  url: "https://github.com/你的帳號",
  position: 1
)
```

建立 Project Link：

```ruby
project.links.create!(
  label: "GitHub Repository",
  url: "https://github.com/你的帳號/專案",
  position: 1
)
```

查詢：

```ruby
profile.links
project.links
```

兩者都使用 Link Model，但分別關聯不同的資料。

### 測試重複技能驗證

```ruby
project.skills << rails_skill
```

由於先前已經建立過相同關聯，這次操作應觸發 ProjectSkill 的唯一性驗證，產生驗證錯誤。

> 使用 `<<` 加入已儲存的關聯時，Rails 會嘗試儲存關聯紀錄；此處預期會因 Validation 失敗而拋出例外。

## 7. 檢查與 Commit

檢查 Rails Autoload：

```bash
bin/rails zeitwerk:check
```

確認 Model、Controller 等類別的命名與檔案路徑符合 Zeitwerk 規則。

檢查 Git：

```bash
git status
git add .
git commit -m "feat(models): add portfolio data models and associations"
```

---

## Git Workflow

**Feature：** Portfolio Data Models

**目標：** 建立 Portfolio 的資料模型、關聯與驗證。

### 開發內容

- 建立 Profile、Skill、Project、ProjectSkill、Link
- 設定 `has_many`、`belongs_to`
- 實作 `has_many :through`
- 實作 Polymorphic Association
- 建立 Model Validation
- 使用 Rails Console 驗證資料與關聯
- 使用 Zeitwerk 檢查類別載入

### Branch

`feature/data-models`

### Commit History

```text
feat(models): add portfolio data models and associations
```
