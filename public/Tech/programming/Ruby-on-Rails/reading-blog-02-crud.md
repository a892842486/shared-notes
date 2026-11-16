2026 10 02 Created
2026 11 16 Moved from peko-discord
---

# macOS Ruby on Rails Blog CRUD 與圖片切換

## 目的

在既有 Bamu Portfolio 中新增 Reading Blog，提供文章管理、標題搜尋、年份分組、精選推薦及多圖片切換功能。

開發內容：

1. 建立 Reading Model
2. 設定前後台 Routes
3. 實作文章排序、搜尋與年份分組
4. 建立前後台 Views
5. 使用 Stimulus 實作圖片左右切換

## 1. 建立 Feature Branch

```bash
git switch main
git pull origin main
git switch -c feature/reading-blog
```

## 2. Model：建立 Reading

### 資料結構

| 欄位 | 型別 | 用途 |
|---|---|---|
| `title` | string | 文章標題，必填 |
| `content` | text | 文章內容，必填 |
| `read_at` | date | 閱讀日期，可空白 |
| `created_at` | datetime | Rails 自動建立 |
| `updated_at` | datetime | Rails 自動更新 |
| `images` | Active Storage | 多張文章圖片 |

### 建立 Model

```bash
bin/rails generate model Reading title:string content:text read_at:date
bin/rails db:migrate
```

`app/models/reading.rb`

```ruby
class Reading < ApplicationRecord
  has_many_attached :images

  validates :title, presence: true
  validates :content, presence: true
end
```

- `has_many_attached`：一篇文章可附加多張圖片。
- `presence: true`：標題與內容不可為空白。
- `read_at`：允許不填寫閱讀日期。

### Rails Console 測試

```bash
bin/rails console
```

```ruby
Reading.create(
  title: "測試文章",
  content: "這是一篇測試文章"
)
```

確認資料成功建立，且 `read_at` 可以是 `nil`。

## 3. Routes：區分前台與後台

`config/routes.rb`

```ruby
resources :readings, only: [:index, :show]

namespace :admin do
  resources :readings, except: [:show]
end
```

| 範圍 | 功能 |
|---|---|
| Public | `index`、`show` |
| Admin | `index`、`new`、`create`、`edit`、`update`、`destroy` |

前台提供文章閱讀，後台負責文章管理。

## 4. Model：日期排序 Scope

`app/models/reading.rb`

```ruby
scope :ordered, -> {
  order(Arel.sql("COALESCE(read_at, DATE(created_at)) DESC, created_at DESC"))
}
```

### COALESCE

SQL `COALESCE` 會由左至右取得第一個非 `NULL` 的值。

```text
有 read_at → 使用 read_at
沒有 read_at → 使用 created_at 的日期
```

- `DATE(created_at)`：將建立時間轉成日期。
- `DESC`：由新到舊排序。
- `created_at DESC`：日期相同時，再依建立時間排序。
- `Arel.sql`：讓 Active Record 將指定字串視為 SQL 表達式。

使用：

```ruby
Reading.ordered
```

> 注意：這個 Scope 會以 `created_at` 補足空白的 `read_at`，但不保證沒有閱讀日期的文章一定排在最後。這與最初規劃略有差異。

## 5. Controller：搜尋、年份分組與精選推薦

建立 Controller：

```bash
bin/rails generate controller admin/readings
bin/rails generate controller Readings
```

`app/controllers/readings_controller.rb`

```ruby
class ReadingsController < ApplicationController
  FEATURED_READING_IDS = [].freeze

  def index
    @search_query = params[:q].to_s.strip

    @readings = Reading.ordered
    @readings = @readings.where("title LIKE ?", "%#{@search_query}%") if @search_query.present?

    @readings_by_year = @readings.to_a.group_by do |reading|
      (reading.read_at || reading.created_at.to_date).year
    end

    @featured_readings = Reading.where(id: FEATURED_READING_IDS).ordered
  end

  def show
    @reading = Reading.find(params[:id])
  end
end
```

### Index 的四個功能

| 程式碼 | 用途 |
|---|---|
| `params[:q]` | 接收搜尋關鍵字 |
| `Reading.ordered` | 文章日期排序 |
| `group_by` | 依年份分組 |
| `FEATURED_READING_IDS` | 指定精選文章 |

### 標題搜尋

```ruby
@search_query = params[:q].to_s.strip

@readings = @readings.where(
  "title LIKE ?", "%#{@search_query}%"
) if @search_query.present?
```

只搜尋文章標題，使用 SQL `LIKE` 進行模糊比對。

### 年份分組

```ruby
@readings_by_year = @readings.to_a.group_by do |reading|
  (reading.read_at || reading.created_at.to_date).year
end
```

將文章依閱讀年份分組；沒有閱讀日期時使用建立日期。

### 精選推薦

```ruby
FEATURED_READING_IDS = [].freeze

@featured_readings = Reading.where(id: FEATURED_READING_IDS).ordered
```

透過指定 Reading ID 選出精選文章。

原始開發階段先保留空陣列，之後再填入實際文章 ID。

## 6. View：前後台頁面

### 後台 Admin

| View | 用途 |
|---|---|
| `admin/readings/index` | 文章列表、新增、編輯、刪除 |
| `admin/readings/new` | 新增文章 |
| `admin/readings/edit` | 編輯文章 |
| `admin/readings/_form` | New / Edit 共用表單 |

建立檔案：

```bash
mkdir -p app/views/readings

touch app/views/admin/readings/index.html.erb
touch app/views/admin/readings/_form.html.erb
touch app/views/admin/readings/new.html.erb
touch app/views/admin/readings/edit.html.erb
touch app/views/readings/index.html.erb
touch app/views/readings/show.html.erb
```

New / Edit 共用：

```erb
<%= render "form", reading: @reading %>
```

這樣表單欄位只需要維護一份。

### 前台 Public

| View | 用途 |
|---|---|
| `readings/index` | 搜尋、精選推薦、年份文章列表 |
| `readings/show` | 文章內容、閱讀日期、圖片畫廊 |

原始 View 包含大量 Tailwind 排版，因此這裡只保留頁面結構與功能，不重複搬運整份 HTML。

## 7. Stimulus：圖片左右切換

建立 Controller：

```bash
touch app/javascript/controllers/reading_gallery_controller.js
```

`app/javascript/controllers/reading_gallery_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["slide", "counter"]

  connect() {
    this.currentIndex = 0
    this.update()
  }

  previous() {
    this.currentIndex =
      (this.currentIndex - 1 + this.slideTargets.length) %
      this.slideTargets.length

    this.update()
  }

  next() {
    this.currentIndex =
      (this.currentIndex + 1) % this.slideTargets.length

    this.update()
  }

  update() {
    this.slideTargets.forEach((slide, index) => {
      slide.classList.toggle("hidden", index !== this.currentIndex)
      slide.classList.toggle("flex", index === this.currentIndex)
    })

    if (this.hasCounterTarget) {
      this.counterTarget.textContent =
        `${this.currentIndex + 1} / ${this.slideTargets.length}`
    }
  }
}
```

### Stimulus 方法

| 方法 | 用途 |
|---|---|
| `connect()` | 初始化圖片索引 |
| `previous()` | 切換上一張 |
| `next()` | 切換下一張 |
| `update()` | 更新圖片顯示與計數器 |

### 循環切換

```javascript
(this.currentIndex + 1) % this.slideTargets.length
```

利用餘數運算 `%`，讓最後一張圖片可以切換回第一張。

上一張圖片也使用相同概念，讓第一張可以返回最後一張。

### 更新畫面

```javascript
slide.classList.toggle("hidden", index !== this.currentIndex)
slide.classList.toggle("flex", index === this.currentIndex)
```

- 目前圖片：顯示。
- 其他圖片：隱藏。

圖片計數器：

```javascript
this.counterTarget.textContent =
  `${this.currentIndex + 1} / ${this.slideTargets.length}`
```

例如：

```text
2 / 5
```

> 這份 Controller 假設至少存在一張 Slide。實際 View 應避免在沒有圖片時啟用上一張、下一張操作。

## 8. Git Workflow

```bash
git add .
git commit -m "feat: add reading blog with image gallery"
```

---

## Git Workflow 摘要

**Feature：** Reading Blog CRUD & Gallery

**目標：** 在 Bamu Portfolio 中新增閱讀文章管理與公開閱讀頁面。

### 開發內容

- 建立 Reading Model、Validation、Active Storage
- 區分前台與後台 Routes
- 使用 SQL `COALESCE` 處理日期排序
- 實作標題搜尋、年份分組、精選推薦
- 建立 Admin CRUD 與共用 Form Partial
- 建立公開 Reading Index / Show
- 使用 Stimulus 實作圖片左右切換

### Branch

`feature/reading-blog`

### Commit History

```text
feat: add reading blog with image gallery
```# Rails 8 Reading Blog CRUD 與圖片切換

---

## 目的

在既有 Bamu Portfolio 中新增 Reading Blog，提供文章管理、標題搜尋、年份分組、精選推薦及多圖片切換功能。

開發內容：

1. 建立 Reading Model
2. 設定前後台 Routes
3. 實作文章排序、搜尋與年份分組
4. 建立前後台 Views
5. 使用 Stimulus 實作圖片左右切換

## 1. 建立 Feature Branch

```bash
git switch main
git pull origin main
git switch -c feature/reading-blog
```

## 2. Model：建立 Reading

### 資料結構

| 欄位 | 型別 | 用途 |
|---|---|---|
| `title` | string | 文章標題，必填 |
| `content` | text | 文章內容，必填 |
| `read_at` | date | 閱讀日期，可空白 |
| `created_at` | datetime | Rails 自動建立 |
| `updated_at` | datetime | Rails 自動更新 |
| `images` | Active Storage | 多張文章圖片 |

### 建立 Model

```bash
bin/rails generate model Reading title:string content:text read_at:date
bin/rails db:migrate
```

`app/models/reading.rb`

```ruby
class Reading < ApplicationRecord
  has_many_attached :images

  validates :title, presence: true
  validates :content, presence: true
end
```

- `has_many_attached`：一篇文章可附加多張圖片。
- `presence: true`：標題與內容不可為空白。
- `read_at`：允許不填寫閱讀日期。

### Rails Console 測試

```bash
bin/rails console
```

```ruby
Reading.create(
  title: "測試文章",
  content: "這是一篇測試文章"
)
```

確認資料成功建立，且 `read_at` 可以是 `nil`。

## 3. Routes：區分前台與後台

`config/routes.rb`

```ruby
resources :readings, only: [:index, :show]

namespace :admin do
  resources :readings, except: [:show]
end
```

| 範圍 | 功能 |
|---|---|
| Public | `index`、`show` |
| Admin | `index`、`new`、`create`、`edit`、`update`、`destroy` |

前台提供文章閱讀，後台負責文章管理。

## 4. Model：日期排序 Scope

`app/models/reading.rb`

```ruby
scope :ordered, -> {
  order(Arel.sql("COALESCE(read_at, DATE(created_at)) DESC, created_at DESC"))
}
```

### COALESCE

SQL `COALESCE` 會由左至右取得第一個非 `NULL` 的值。

```text
有 read_at → 使用 read_at
沒有 read_at → 使用 created_at 的日期
```

- `DATE(created_at)`：將建立時間轉成日期。
- `DESC`：由新到舊排序。
- `created_at DESC`：日期相同時，再依建立時間排序。
- `Arel.sql`：讓 Active Record 將指定字串視為 SQL 表達式。

使用：

```ruby
Reading.ordered
```

> 注意：這個 Scope 會以 `created_at` 補足空白的 `read_at`，但不保證沒有閱讀日期的文章一定排在最後。這與最初規劃略有差異。

## 5. Controller：搜尋、年份分組與精選推薦

建立 Controller：

```bash
bin/rails generate controller admin/readings
bin/rails generate controller Readings
```

`app/controllers/readings_controller.rb`

```ruby
class ReadingsController < ApplicationController
  FEATURED_READING_IDS = [].freeze

  def index
    @search_query = params[:q].to_s.strip

    @readings = Reading.ordered
    @readings = @readings.where("title LIKE ?", "%#{@search_query}%") if @search_query.present?

    @readings_by_year = @readings.to_a.group_by do |reading|
      (reading.read_at || reading.created_at.to_date).year
    end

    @featured_readings = Reading.where(id: FEATURED_READING_IDS).ordered
  end

  def show
    @reading = Reading.find(params[:id])
  end
end
```

### Index 的四個功能

| 程式碼 | 用途 |
|---|---|
| `params[:q]` | 接收搜尋關鍵字 |
| `Reading.ordered` | 文章日期排序 |
| `group_by` | 依年份分組 |
| `FEATURED_READING_IDS` | 指定精選文章 |

### 標題搜尋

```ruby
@search_query = params[:q].to_s.strip

@readings = @readings.where(
  "title LIKE ?", "%#{@search_query}%"
) if @search_query.present?
```

只搜尋文章標題，使用 SQL `LIKE` 進行模糊比對。

### 年份分組

```ruby
@readings_by_year = @readings.to_a.group_by do |reading|
  (reading.read_at || reading.created_at.to_date).year
end
```

將文章依閱讀年份分組；沒有閱讀日期時使用建立日期。

### 精選推薦

```ruby
FEATURED_READING_IDS = [].freeze

@featured_readings = Reading.where(id: FEATURED_READING_IDS).ordered
```

透過指定 Reading ID 選出精選文章。

原始開發階段先保留空陣列，之後再填入實際文章 ID。

## 6. View：前後台頁面

### 後台 Admin

| View | 用途 |
|---|---|
| `admin/readings/index` | 文章列表、新增、編輯、刪除 |
| `admin/readings/new` | 新增文章 |
| `admin/readings/edit` | 編輯文章 |
| `admin/readings/_form` | New / Edit 共用表單 |

建立檔案：

```bash
mkdir -p app/views/readings

touch app/views/admin/readings/index.html.erb
touch app/views/admin/readings/_form.html.erb
touch app/views/admin/readings/new.html.erb
touch app/views/admin/readings/edit.html.erb
touch app/views/readings/index.html.erb
touch app/views/readings/show.html.erb
```

New / Edit 共用：

```erb
<%= render "form", reading: @reading %>
```

這樣表單欄位只需要維護一份。

### 前台 Public

| View | 用途 |
|---|---|
| `readings/index` | 搜尋、精選推薦、年份文章列表 |
| `readings/show` | 文章內容、閱讀日期、圖片畫廊 |

原始 View 包含大量 Tailwind 排版，因此這裡只保留頁面結構與功能，不重複搬運整份 HTML。

## 7. Stimulus：圖片左右切換

建立 Controller：

```bash
touch app/javascript/controllers/reading_gallery_controller.js
```

`app/javascript/controllers/reading_gallery_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["slide", "counter"]

  connect() {
    this.currentIndex = 0
    this.update()
  }

  previous() {
    this.currentIndex =
      (this.currentIndex - 1 + this.slideTargets.length) %
      this.slideTargets.length

    this.update()
  }

  next() {
    this.currentIndex =
      (this.currentIndex + 1) % this.slideTargets.length

    this.update()
  }

  update() {
    this.slideTargets.forEach((slide, index) => {
      slide.classList.toggle("hidden", index !== this.currentIndex)
      slide.classList.toggle("flex", index === this.currentIndex)
    })

    if (this.hasCounterTarget) {
      this.counterTarget.textContent =
        `${this.currentIndex + 1} / ${this.slideTargets.length}`
    }
  }
}
```

### Stimulus 方法

| 方法 | 用途 |
|---|---|
| `connect()` | 初始化圖片索引 |
| `previous()` | 切換上一張 |
| `next()` | 切換下一張 |
| `update()` | 更新圖片顯示與計數器 |

### 循環切換

```javascript
(this.currentIndex + 1) % this.slideTargets.length
```

利用餘數運算 `%`，讓最後一張圖片可以切換回第一張。

上一張圖片也使用相同概念，讓第一張可以返回最後一張。

### 更新畫面

```javascript
slide.classList.toggle("hidden", index !== this.currentIndex)
slide.classList.toggle("flex", index === this.currentIndex)
```

- 目前圖片：顯示。
- 其他圖片：隱藏。

圖片計數器：

```javascript
this.counterTarget.textContent =
  `${this.currentIndex + 1} / ${this.slideTargets.length}`
```

例如：

```text
2 / 5
```

> 這份 Controller 假設至少存在一張 Slide。實際 View 應避免在沒有圖片時啟用上一張、下一張操作。

## 8. Git Workflow

```bash
git add .
git commit -m "feat: add reading blog with image gallery"
```

---

## Git Workflow 摘要

**Feature：** Reading Blog CRUD & Gallery

**目標：** 在 Bamu Portfolio 中新增閱讀文章管理與公開閱讀頁面。

### 開發內容

- 建立 Reading Model、Validation、Active Storage
- 區分前台與後台 Routes
- 使用 SQL `COALESCE` 處理日期排序
- 實作標題搜尋、年份分組、精選推薦
- 建立 Admin CRUD 與共用 Form Partial
- 建立公開 Reading Index / Show
- 使用 Stimulus 實作圖片左右切換

### Branch

`feature/reading-blog`

### Commit History

```text
feat: add reading blog with image gallery
```