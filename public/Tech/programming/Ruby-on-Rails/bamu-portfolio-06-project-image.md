2026 09 18 Created
2026 11 16 Moved from peko-discord
---

# macOS Ruby on Rails Project ProjectImage 圖片與說明管理

## 目的

讓 Portfolio 的每張作品圖片都能擁有獨立的說明文字（Caption），並支援新增、編輯、刪除及前台展示。

原本：

```text
Project
  └── has_many_attached :images
```

修改為：

```text
Project
  └── has_many :project_images
        ├── caption
        ├── position
        └── has_one_attached :image
```

**核心差異：** 原本只管理圖片附件，現在將「圖片 + 說明 + 排序欄位」視為一筆獨立資料。

## 1. 建立 ProjectImage Model

```bash
bin/rails generate model ProjectImage project:references caption:string position:integer
bin/rails db:migrate
```

### Model 關聯

`app/models/project_image.rb`

```ruby
has_one_attached :image
```

`app/models/project.rb`

```ruby
has_many :project_images, dependent: :destroy
```

- `has_one_attached`：每筆 ProjectImage 附加一張圖片。
- `has_many :project_images`：每個 Project 可以擁有多筆圖片資料。
- `dependent: :destroy`：刪除 Project 時一併刪除其 ProjectImage 紀錄。

`project:references` 產生 Project 外鍵與關聯欄位；產生器建立的 `ProjectImage` Model 也會包含 `belongs_to :project`。

## 2. Nested Attributes：讓 Project 管理圖片

`app/models/project.rb`

```ruby
accepts_nested_attributes_for :project_images, allow_destroy: true
```

讓 Project 表單可以一起新增、更新或刪除關聯的 ProjectImage。

### Strong Parameters

`app/controllers/admin/projects_controller.rb`

```ruby
project_images_attributes: [
  :id,
  :caption,
  :image,
  :_destroy
]
```

| 參數 | 用途 |
|---|---|
| `:id` | 識別既有 ProjectImage |
| `:caption` | 圖片說明 |
| `:image` | 上傳圖片 |
| `:_destroy` | 標記刪除關聯資料 |

> 原始 Migration 有建立 `position`，但最終紀錄中的 Strong Parameters 已移除 `:position`，因此這裡依照最終版本保留。

### Form：圖片與說明

`app/views/admin/projects/_form.html.erb`

在 ProjectImage 的 Nested Form 中：

```erb
<%= image_form.label :image, "Image" %>
<%= image_form.file_field :image %>
```

搭配既有的 Caption 欄位：

```erb
<%= image_form.label :caption, "Caption" %>
<%= image_form.text_field :caption %>
```

以上是 Nested Form 內的欄位片段，實際仍需搭配 `fields_for :project_images` 與新增、刪除欄位的表單結構。

原始紀錄也有建立：

```bash
bin/rails generate stimulus project-images
```

用於管理後台動態新增圖片欄位；但沒有提供 Stimulus Controller 的實際內容，因此本篇不推測其實作。

## 3. 前台：顯示圖片與說明

`app/controllers/projects_controller.rb`

```ruby
def index
  @projects = Project.all
end

def show
  @project = Project.find(params[:id])
end
```

`config/routes.rb`

```ruby
resources :projects, only: [:index, :show]
```

### Show：圖片與 Caption

`app/views/projects/show.html.erb`

```erb
<h2 class="mt-10 text-2xl font-semibold">Screenshots</h2>

<div class="mt-4 space-y-6">
  <% @project.project_images.order(:created_at).each do |project_image| %>
    <% if project_image.image.attached? %>
      <div>
        <%= image_tag project_image.image,
                      class: "w-full max-w-4xl rounded-lg" %>

        <p><%= project_image.caption %></p>
      </div>
    <% end %>
  <% end %>
</div>
```

- `order(:created_at)`：依照建立時間排列圖片。
- `image.attached?`：確認附件存在。
- `image_tag`：顯示圖片。
- `caption`：顯示對應說明。

> 原始 Show 紀錄中有 `image_tag project_image.image` 的變數誤植；此處已修正。

### Index：顯示封面圖片

`app/views/projects/index.html.erb`

```erb
<% cover_image = project.project_images.order(:created_at).first %>

<% if cover_image&.image&.attached? %>
  <%= link_to project_path(project) do %>
    <%= image_tag cover_image.image,
                  class: "mt-4 w-full rounded-lg" %>
  <% end %>
<% end %>
```

只顯示第一張圖片作為作品封面，點擊後進入 Project Show。

`&.` 是 Ruby Safe Navigation Operator，避免沒有圖片時直接呼叫 `image` 造成錯誤。

## 4. 前台共用 Partial

開發過程也完成了 Project Index / Show 的 Tailwind 排版，並將重複元件抽成 Partial。

### Skill Badge

`app/views/shared/_badge.html.erb`

```erb
<span class="rounded-full bg-gray-100 px-3 py-1 text-sm text-gray-600">
  <%= text %>
</span>
```

使用：

```erb
<%= render "shared/badge", text: skill.name %>
```

### Project Link

`app/views/shared/_link.html.erb`

將作品連結統一成帶有外連圖示的膠囊按鈕。

使用：

```erb
<%= render "shared/link", link: link %>
```

這樣 Index / Show 不必重複撰寫相同的樣式。

## 5. 清理舊圖片架構

改用 ProjectImage 後，移除舊的附件管理方式。

### Model

`app/models/project.rb`

移除：

```ruby
has_many_attached :images
validate :images_must_be_images
```

以及原本的 `images_must_be_images` 方法。

### Controller

`app/controllers/admin/projects_controller.rb`

恢復使用：

```ruby
if @project.update(project_params)
```

移除舊的：

```ruby
@project.images.attach(...)
```

以及 `destroy_image` Action。

### Routes

移除：

```ruby
delete :destroy_image, on: :member
```

保留原本的 Project Resource Routes。

### Views

將原本：

```erb
project.images
```

改為使用：

```erb
project.project_images
```

並透過：

```erb
project_image.image
```

取得實際附件。

> 這裡整理的是程式碼架構遷移。原始紀錄沒有提供舊附件資料搬遷程序，因此不能直接視為已完成既有圖片資料的轉移。

## 6. 測試與 Git Workflow

開發時也調整 Fixtures，避免測試資料名稱衝突。

`test/fixtures/projects.yml`

```yaml
one:
  name: Test Project
  summary: Test summary
  description: Test description

two:
  name: Another Project
  summary: Another summary
  description: Another description
```

前台 Controller 測試：

`test/controllers/projects_controller_test.rb`

```ruby
test "should get index" do
  get projects_url
  assert_response :success
end

test "should get show" do
  get project_url(projects(:one))
  assert_response :success
end
```

執行：

```bash
bin/rails test
```

### Commit

```bash
git add .
git commit -m "refactor(ProjectImage): migrate project images to ProjectImage"
```

### Merge

```bash
git push -u origin feature/project-images

git checkout main
git pull origin main
git merge feature/project-images
git push origin main
```

---

## Git Workflow 摘要

**Feature：** Project Images & Caption

**目標：** 將 Project 圖片改為獨立 Model，支援圖片說明與前台展示。

### 開發內容

- 建立 ProjectImage Model
- 使用 `has_one_attached` 管理單張附件
- 使用 Nested Attributes 管理關聯資料
- 建立圖片與 Caption 欄位
- 前台 Index 顯示作品封面
- 前台 Show 顯示圖片與說明
- 抽出 Badge / Link Partial
- 清理舊 Active Storage 架構
- 補充前台 Controller 測試

### Branch

`feature/project-images`

### Commit History

```text
Add project images and public project pages
refactor(ProjectImage): migrate project images to ProjectImage
```
