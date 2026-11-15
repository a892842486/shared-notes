2026 02 06 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 後台訂單狀態管理（AASM）

## 目的

建立後台訂單管理頁面，讓管理員查看訂單列表與明細，並透過 AASM Event 操作訂單狀態。

本篇延續前一篇「訂單狀態機（AASM）」，重點在於將狀態機整合進 Rails MVC。

- Admin Namespace 與自訂 Member Routes
- Controller 執行 AASM Event
- Devise 後台權限控制
- Helper 顯示訂單狀態
- View 根據狀態顯示操作按鈕
- 共用訂單明細 Partial

## 1. 建立 Git 分支

```bash
git checkout -b feat/order-state-admin
```

## 2. 建立 Controller 與 Routes

建立後台 Controller：

```bash
bin/rails generate controller admin/orders
```

`config/routes.rb`

```ruby
namespace :admin do
  resources :products

  resources :orders do
    member do
      post :cancel
      post :ship
      post :shipped
      post :return
    end
  end
end
```

### Member Routes

`member` 用來建立針對單筆訂單的自訂操作路由。

| Route Helper | HTTP Method | Controller Action |
|---|---|---|
| `cancel_admin_order_path` | POST | `cancel` |
| `ship_admin_order_path` | POST | `ship` |
| `shipped_admin_order_path` | POST | `shipped` |
| `return_admin_order_path` | POST | `return` |

查看路由：

```bash
bin/rails routes -g admin_orders
```

> `link_to` 預設發送 GET 請求。需要 POST 操作時，可以使用 `button_to`，由 Rails 產生對應的 HTML Form。

## 3. Controller：後台訂單狀態操作

`app/controllers/admin/orders_controller.rb`

```ruby
class Admin::OrdersController < ApplicationController
  layout "admin"

  before_action :authenticate_user!
  before_action :admin_required
  before_action :set_order,
                only: [:show, :ship, :shipped, :cancel, :return]

  def index
    @orders = Order.order(id: :desc)
  end

  def show
  end

  def ship
    @order.ship!
    redirect_back fallback_location: admin_orders_path,
                  notice: "訂單已出貨，通知信已寄出"
  end

  def shipped
    @order.deliver!
    redirect_back fallback_location: admin_orders_path
  end

  def cancel
    @order.cancel_order!
    redirect_back fallback_location: admin_orders_path,
                  notice: "訂單已取消，通知信已寄出"
  end

  def return
    @order.return_good!
    redirect_back fallback_location: admin_orders_path
  end

  private

  def set_order
    @order = Order.find_by!(token: params[:id])
  end
end
```

### AASM Event 對照

| Controller Action | AASM Event | 狀態轉換 |
|---|---|---|
| `ship` | `ship!` | paid → shipping |
| `shipped` | `deliver!` | shipping → shipped |
| `cancel` | `cancel_order!` | order_placed / paid → order_cancelled |
| `return` | `return_good!` | shipped → good_returned |

使用 AASM Event 操作狀態，而不是直接修改 `aasm_state` 欄位，確保狀態轉換遵守 Model 定義的規則。

### 權限控制

```ruby
before_action :authenticate_user!
before_action :admin_required
```

- `authenticate_user!`：Devise 提供，要求使用者先登入。
- `admin_required`：專案自行實作，檢查使用者是否具備管理員權限。

> `admin_required` 並非 Devise 內建方法，必須先在專案中定義。

### Token 查詢訂單

```ruby
def set_order
  @order = Order.find_by!(token: params[:id])
end
```

使用訂單 Token 查詢，而非資料庫 ID。

因此，View 產生訂單路由時，也必須傳入對應 Token：

```erb
<%= link_to order.id, admin_order_path(order.token) %>
```

> 如果 `Order#to_param` 已設定回傳 Token，也可以直接使用 `admin_order_path(order)`。

### redirect_back

```ruby
redirect_back fallback_location: admin_orders_path
```

操作完成後返回上一頁；若沒有可用的上一頁資訊，就導向後台訂單列表。

```bash
git add .
git commit -m "feat(admin/orders): add admin order management routes and controller"
```

## 4. Helper：訂單狀態顯示

`app/helpers/orders_helper.rb`

```ruby
module OrdersHelper
  def order_state_badge(order)
    base = "px-3 py-1 rounded-full text-xs font-semibold"

    case order.aasm_state.to_sym
    when :order_placed
      "#{base} bg-gray-100 text-gray-600"
    when :paid
      "#{base} bg-blue-100 text-blue-600"
    when :shipping
      "#{base} bg-yellow-100 text-yellow-700"
    when :shipped
      "#{base} bg-green-100 text-green-600"
    when :order_cancelled
      "#{base} bg-red-100 text-red-600"
    when :good_returned
      "#{base} bg-purple-100 text-purple-600"
    else
      "#{base} bg-gray-100"
    end
  end

  def order_state_text(order)
    t("aasm.order.#{order.aasm_state}")
  end
end
```

### Helper 的用途

- `order_state_badge`：根據訂單狀態回傳不同的 Tailwind CSS Class。
- `order_state_text`：透過 Rails I18n 顯示對應的狀態文字。

例如：

```erb
<span class="<%= order_state_badge(order) %>">
  <%= order_state_text(order) %>
</span>
```

> `order_state_text` 需要事先設定對應的 I18n 翻譯。若未設定，可能顯示 Missing Translation。

## 5. Views：訂單列表

建立 View：

```bash
touch app/views/admin/orders/index.html.erb
```

`app/views/admin/orders/index.html.erb`

```erb
<h2>訂單列表</h2>

<table>
  <thead>
    <tr>
      <th>訂單編號</th>
      <th>建立時間</th>
      <th>訂購者</th>
      <th>訂單狀態</th>
      <th>操作</th>
    </tr>
  </thead>

  <tbody>
    <% @orders.each do |order| %>
      <tr>
        <td>
          <%= link_to order.id, admin_order_path(order.token) %>
        </td>

        <td><%= l(order.created_at, format: :long) %></td>
        <td><%= order.user.email %></td>

        <td>
          <span class="<%= order_state_badge(order) %>">
            <%= order_state_text(order) %>
          </span>
        </td>

        <td>
          <% if order.may_ship? %>
            <%= button_to "出貨",
                ship_admin_order_path(order.token),
                method: :post %>
          <% end %>

          <% if order.may_deliver? %>
            <%= button_to "完成配送",
                shipped_admin_order_path(order.token),
                method: :post %>
          <% end %>

          <% if order.may_cancel_order? %>
            <%= button_to "取消",
                cancel_admin_order_path(order.token),
                method: :post,
                data: { turbo_confirm: "確定要取消嗎？" } %>
          <% end %>
        </td>
      </tr>
    <% end %>
  </tbody>
</table>
```

### may_ship?：狀態判斷

AASM 會根據 Event 產生對應的狀態檢查方法。

```ruby
order.may_ship?
order.may_deliver?
order.may_cancel_order?
```

例如：

```erb
<% if order.may_ship? %>
  <%= button_to "出貨",
      ship_admin_order_path(order.token),
      method: :post %>
<% end %>
```

只有目前允許執行 `ship` Event 的訂單，才會顯示「出貨」按鈕。

> View 的狀態判斷只負責控制 UI。後端仍需要透過 AASM 與權限驗證限制操作。

## 6. Views：訂單明細

建立 View：

```bash
touch app/views/admin/orders/show.html.erb
```

`app/views/admin/orders/show.html.erb`

```erb
<div class="max-w-5xl mx-auto px-4 py-6">

  <!-- 標題與狀態 -->
  <div class="flex justify-between items-center mb-6">
    <h1 class="text-2xl font-bold">
      訂單 #<%= @order.id %>
    </h1>

    <span class="<%= order_state_badge(@order) %>">
      <%= @order.aasm_state.humanize %>
    </span>
  </div>

  <!-- 訂單資訊 -->
  <div class="grid grid-cols-2 gap-4 mb-6">
    <div class="p-4 border rounded-lg">
      <p>建立時間：<%= @order.created_at.strftime("%Y-%m-%d %H:%M") %></p>
      <p>狀態：<%= @order.aasm_state.humanize %></p>
    </div>

    <div class="p-4 border rounded-lg">
      <p><%= @order.billing_name %></p>
      <p><%= @order.billing_address %></p>
    </div>
  </div>

  <!-- 共用訂單明細 -->
  <%= render "orders/order_detail", order: @order %>

  <!-- 管理員操作 -->
  <div class="mt-6 flex gap-3 justify-end">

    <% if @order.may_make_payment? %>
      <%= button_to "標記已付款", "#",
          method: :post,
          disabled: true,
          class: "px-4 py-2 bg-gray-400 text-white rounded" %>
    <% end %>

    <% if @order.may_ship? %>
      <%= button_to "出貨",
          ship_admin_order_path(@order.token),
          method: :post,
          class: "px-4 py-2 bg-blue-600 text-white rounded" %>
    <% end %>

    <% if @order.may_deliver? %>
      <%= button_to "完成配送",
          shipped_admin_order_path(@order.token),
          method: :post,
          class: "px-4 py-2 bg-green-600 text-white rounded" %>
    <% end %>

    <% if @order.may_cancel_order? %>
      <%= button_to "取消訂單",
          cancel_admin_order_path(@order.token),
          method: :post,
          data: { turbo_confirm: "確定要取消嗎？" },
          class: "px-4 py-2 bg-red-600 text-white rounded" %>
    <% end %>

  </div>
</div>
```

### 共用 Partial

```erb
<%= render "orders/order_detail", order: @order %>
```

呼叫既有的 `_order_detail.html.erb`，顯示訂單商品快照、金額等資訊，避免在不同頁面重複實作。

> 這個 Partial 來自原本的訂單功能，並非本篇新建立的檔案。

## 7. 整合後台導覽

`app/views/layouts/admin.html.erb`

在既有的 Products 連結旁新增 Orders：

```erb
<%= link_to "Products", admin_products_path %>
<%= link_to "Orders", admin_orders_path %>
```

讓管理員可以透過後台導覽切換商品管理與訂單管理。

```bash
git add .
git commit -m "feat(admin/orders): add admin order list and detail pages with state actions"
```

## 8. 功能驗證

啟動 Rails：

```bash
bin/dev
```

測試後台訂單管理：

- 管理員可以進入訂單列表。
- 管理員可以查看單筆訂單明細。
- 頁面顯示訂購者、商品快照、金額與訂單狀態。
- 訂單狀態依照不同 Badge 顏色顯示。
- 訂單處於 `paid` 時，可以執行出貨。
- 訂單處於 `shipping` 時，可以完成配送。
- 訂單符合取消條件時，可以取消訂單。
- 未登入或非管理員不可操作後台訂單。

---

## Git Workflow

**Feature：** Order State Admin（後台訂單狀態管理）

**目標：** 建立後台訂單管理頁面，透過 AASM Event 管理訂單狀態。

### 開發內容

- 建立後台訂單管理 Routes
- 實作 `Admin::OrdersController`
- 整合 Devise 與管理員權限驗證
- 建立訂單列表與明細頁面
- 顯示訂單商品快照與顧客資訊
- 使用 Helper 顯示訂單狀態 Badge
- 透過 `may_*?` 判斷可執行的 AASM Event
- 使用 `button_to` 執行狀態操作
- 整合 Admin Layout 導覽

### Branch

`feat/order-state-admin`

### Commit History

```text
feat(admin/orders): add admin order management routes and controller
feat(admin/orders): add admin order list and detail pages with state actions
```
