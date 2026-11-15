2026 01 21 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails CRUD-admin

## 目的

使用 Rails MVC 架構建立後台商品管理功能，完成商品的新增、瀏覽、編輯、刪除（CRUD）。

本篇重點：

- RESTful Routes 與 Namespace
- Active Record 資料庫操作
- Controller CRUD Actions
- Strong Parameters
- 共用 `_form` Partial
- Turbo 刪除確認

## 1. 建立 Git 分支

```bash
git checkout -b feat/admin-products
```

## 2. 建立 Controller 與 Routes

建立後台 Controller：

```bash
bin/rails generate controller admin/products
```

`config/routes.rb`

```ruby
Rails.application.routes.draw do
  namespace :admin do
    resources :products
  end
end
```

`namespace :admin` 會產生 `/admin/products` 等後台路由，並對應 `Admin::ProductsController`。

查看路由：

```bash
bin/rails routes -g admin
```

## 3. 建立 Product Model

```bash
bin/rails generate model Product title:string description:text quantity:integer price:integer
bin/rails db:migrate
```

資料欄位：

| 欄位 | 型別 | 用途 |
|---|---|---|
| `title` | string | 商品名稱 |
| `description` | text | 商品描述 |
| `quantity` | integer | 商品庫存 |
| `price` | integer | 商品價格 |

```bash
git add .
git commit -m "feat(admin/products): set up admin product resource"
```

## 4. 實作 Controller CRUD

`app/controllers/admin/products_controller.rb`

```ruby
class Admin::ProductsController < ApplicationController
  def index
    @products = Product.order(created_at: :desc)
  end

  def new
    @product = Product.new
  end

  def create
    @product = Product.new(product_params)

    if @product.save
      redirect_to admin_products_path, notice: "Product created"
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
    @product = Product.find(params[:id])
  end

  def update
    @product = Product.find(params[:id])

    if @product.update(product_params)
      redirect_to admin_products_path, notice: "Product updated"
    else
      render :edit, status: :unprocessable_entity
    end
  end

  def destroy
    @product = Product.find(params[:id])
    @product.destroy

    redirect_to admin_products_path, notice: "Product deleted"
  end

  private

  def product_params
    params.require(:product).permit(
      :title,
      :description,
      :quantity,
      :price
    )
  end
end
```

### CRUD Actions 對照

| Action | HTTP Method | 用途 |
|---|---|---|
| `index` | GET | 顯示商品列表 |
| `new` | GET | 顯示新增表單 |
| `create` | POST | 建立商品 |
| `edit` | GET | 顯示編輯表單 |
| `update` | PATCH / PUT | 更新商品 |
| `destroy` | DELETE | 刪除商品 |

### Strong Parameters

```ruby
def product_params
  params.require(:product).permit(
    :title, :description, :quantity, :price
  )
end
```

只允許指定欄位透過表單進行批次賦值，避免使用者任意修改其他 Model 屬性。

### 表單驗證失敗

```ruby
render :new, status: :unprocessable_entity
```

當資料儲存失敗時，重新顯示表單並回傳 HTTP `422`，讓 Rails / Turbo 正確處理失敗回應。

```bash
git add .
git commit -m "feat(admin/products): implement product management flow"
```

## 5. 建立 Views

```text
app/views/admin/products/
├── index.html.erb    # 商品列表
├── new.html.erb      # 新增商品
├── edit.html.erb     # 編輯商品
└── _form.html.erb    # 共用表單
```

### 共用表單 Partial

`app/views/admin/products/_form.html.erb`

使用 `form_with` 自動判斷商品是新增還是編輯，避免重複維護兩份表單。

```erb
<h2>
  <%= product.new_record? ? "New Product" : "Edit Product" %>
</h2>

<%= form_with model: [:admin, product] do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>

  <div>
    <%= f.label :description %>
    <%= f.text_area :description, rows: 4 %>
  </div>

  <div>
    <%= f.label :quantity %>
    <%= f.number_field :quantity %>
  </div>

  <div>
    <%= f.label :price %>
    <%= f.number_field :price %>
  </div>

  <%= f.submit product.new_record? ? "Create Product" : "Update Product" %>
<% end %>
```

`model: [:admin, product]` 會根據 Model 狀態，自動選擇對應的後台路由與 HTTP Method。

### 新增商品

`app/views/admin/products/new.html.erb`

```erb
<%= render "form", product: @product %>
```

### 編輯商品

`app/views/admin/products/edit.html.erb`

```erb
<%= render "form", product: @product %>
```

兩個頁面共用 `_form.html.erb`，修改表單時只需要維護一個檔案。

## 6. 商品列表與刪除功能

`app/views/admin/products/index.html.erb`

```erb
<h1>商品管理</h1>

<%= link_to "新增商品", new_admin_product_path %>

<% @products.each do |product| %>
  <div>
    <h2><%= product.title %></h2>
    <p><%= product.price %> 元</p>

    <%= link_to "編輯", edit_admin_product_path(product) %>

    <%= button_to "刪除",
        admin_product_path(product),
        method: :delete,
        data: { turbo_confirm: "確定要刪除這個商品嗎？" } %>
  </div>
<% end %>
```

### 為什麼刪除使用 `button_to`？

`button_to` 會產生 HTML Form，可以直接送出 `DELETE` 請求。

搭配：

```ruby
method: :delete,
data: { turbo_confirm: "確定要刪除這個商品嗎？" }
```

讓使用者刪除前先確認，避免誤操作。

```bash
git add .
git commit -m "feat(ui): enhance admin product management interface"
```

## 7. 功能驗證

啟動 Rails：

```bash
bin/dev
```

開啟 `/admin/products`，依序測試：

- 商品列表：可以顯示所有商品。
- 新增商品：成功建立資料。
- 編輯商品：可以修改既有資料。
- 刪除商品：顯示確認視窗並成功刪除。
- 表單驗證失敗：若 Model 有設定 Validation，確認能重新顯示表單。

> 本篇尚未實作後台權限限制，`namespace :admin` 只負責路由與 Controller 分類，不會自動阻止一般使用者進入後台。

---

## Git Workflow

**Feature：** Admin Product Management

**目標：** 建立後台商品 CRUD，完成 Model、Controller、Views 與 Routes。

### 開發內容

- 建立 Product Model 與資料表
- 建立 Admin Namespace 與 Routes
- 實作 CRUD Actions
- 使用 Strong Parameters
- 共用 `_form` Partial
- 使用 HTTP 422 處理表單失敗回應
- 使用 Turbo Confirm 處理刪除確認
- 使用 Tailwind CSS 客製化後台介面

### Branch

`feat/admin-products`

### Commit History

```text
feat(admin/products): set up admin product resource
feat(admin/products): implement product management flow
feat(ui): enhance admin product management interface
```
