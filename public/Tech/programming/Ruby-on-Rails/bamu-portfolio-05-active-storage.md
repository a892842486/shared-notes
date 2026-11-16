2026 09 18 Created
2026 11 16 Moved from peko-discord
---

# macOS Ruby on Rails Project Active Storage 圖片上傳

## 目的

使用 Rails Active Storage，讓 Portfolio 的 Project 可以上傳與顯示圖片。

本篇記錄 Active Storage 的基本使用方式；後續為了支援圖片說明文字，改由 `ProjectImage` Model 管理圖片。

## 1. 建立 Feature Branch

```bash
git switch main
git pull
git switch -c feature/project-images
```

## 2. 安裝 Active Storage

```bash
bin/rails active_storage:install
bin/rails db:migrate
```

Active Storage 是 Rails 內建的檔案附件功能。

安裝後會建立管理附件所需的資料表，例如：

- `active_storage_blobs`：檔案資訊。
- `active_storage_attachments`：附件與 Model 的關聯。
- `active_storage_variant_records`：圖片變體相關紀錄。

Rails 8 新專案可能已經安裝，執行前先確認現有 Migration，避免重複安裝。

## 3. Model：附加多張圖片

`app/models/project.rb`

```ruby
has_many_attached :images
```

讓每個 Project 可以附加多張圖片。

例如：

```text
Project
  ├── image_1.png
  ├── image_2.png
  └── image_3.png
```

這是最初使用的架構，後續已改為 `ProjectImage`。

## 4. View：上傳與顯示圖片

`app/views/admin/projects/_form.html.erb`

```erb
<% project.images.each do |image| %>
  <%= image_tag image, width: 300 %>
<% end %>

<div>
  <%= form.label :images %>
  <%= form.file_field :images, multiple: true %>
</div>
```

- `image_tag`：顯示已上傳圖片。
- `file_field`：產生檔案選擇欄位。
- `multiple: true`：允許一次選取多張圖片。

### Strong Parameters

`app/controllers/admin/projects_controller.rb`

```ruby
params.require(:project).permit(
  :name,
  :summary,
  :description,
  images: []
)
```

`images: []` 允許表單傳入多個圖片附件。

> 此處只展示圖片相關參數；實際專案還有 Skills、Links 等其他欄位。

## 5. Controller：刪除圖片

最初使用獨立 Route 刪除附件。

`config/routes.rb`

```ruby
namespace :admin do
  resources :projects do
    delete :destroy_image, on: :member
  end
end
```

`app/controllers/admin/projects_controller.rb`

```ruby
def destroy_image
  @project = Project.find(params[:id])
  image = @project.images.attachments.find(params[:image_id])
  image.purge

  redirect_to edit_admin_project_path(@project)
end
```

### purge

```ruby
image.purge
```

刪除 Active Storage 附件及其儲存的檔案（在沒有其他附件共用該 Blob 的一般情況下）。

View 刪除按鈕：

```erb
<%= button_to "Delete",
              destroy_image_admin_project_path(project),
              method: :delete,
              params: { image_id: image.id } %>
```

## 6. 附件追加與檔案驗證

### 追加圖片

最初的更新邏輯：

```ruby
if @project.update(project_params.except(:images))
  @project.images.attach(params[:project][:images]) if params[:project][:images].present?
end
```

目的：讓新上傳的圖片追加到既有附件，而不是取代原有圖片。

### 限制圖片格式

`app/models/project.rb`

```ruby
validate :images_must_be_images

private

def images_must_be_images
  images.each do |image|
    unless image.content_type.start_with?("image/")
      errors.add(:images, "must be an image")
    end
  end
end
```

檢查附件宣告的 MIME Type 是否以 `image/` 開頭。

> MIME Type 不能單獨保證檔案內容安全；正式服務仍應依需求搭配檔案大小、格式及內容驗證。

---

## 開發結果

完成 Active Storage 的基本功能：

- Project 多圖片上傳
- 後台圖片預覽
- 刪除圖片
- 追加圖片
- 圖片類型驗證

**後續架構變更：** 因為需要替每張圖片加入 Caption，改用 `ProjectImage` Model 管理附件。上述 `has_many_attached :images` 與獨立刪除 Route 已在後續重構中移除。
