2026 02 06 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 訂單狀態機（AASM）

## 目的

使用 **AASM（Acts As State Machine）** 管理電商訂單狀態，定義允許的狀態轉換規則，避免訂單直接跳過必要流程。

本篇重點：

- AASM 狀態機
- 訂單狀態與 Event
- Migration 狀態欄位
- Model 狀態轉換規則
- 付款流程整合
- Controller 透過 Event 操作

## 1. 建立 Git 分支

```bash
git checkout -b feat/order-state-machine
```

## 2. 安裝 AASM

在 `Gemfile` 加入：

```ruby
gem "aasm"
```

安裝依賴：

```bash
bundle install
```

## 3. 建立訂單狀態欄位

產生 Migration：

```bash
bin/rails generate migration AddAasmStateToOrders aasm_state:string
```

Rails 會自動在 db/migrate/ 產生 Migration 檔案：

```bash
db/migrate/20260316123456_add_aasm_state_to_orders.rb
```

> 前面的數字是 Rails 產生的時間戳記，每次建立都不同。

開啟該檔案，修改欄位設定：

```ruby
class AddAasmStateToOrders < ActiveRecord::Migration[8.0]
  def change
    add_column :orders, :aasm_state, :string,
               default: "order_placed", null: false

    add_index :orders, :aasm_state
  end
end
```

> `ActiveRecord::Migration[8.0]` 為示意，實際版本應以 Rails 產生的 Migration 為準。

欄位設定：

| 設定 | 用途 |
|---|---|
| `aasm_state` | 儲存訂單目前狀態 |
| `default: "order_placed"` | 新訂單預設狀態 |
| `null: false` | 避免狀態欄位為 NULL |
| `add_index` | 建立狀態欄位索引 |

執行 Migration：

```bash
bin/rails db:migrate
```

## 4. Model：定義訂單狀態機

`app/models/order.rb`

```ruby
class Order < ApplicationRecord
  include AASM

  aasm column: :aasm_state do
    state :order_placed, initial: true
    state :paid
    state :shipping
    state :shipped
    state :order_cancelled
    state :good_returned

    event :make_payment do
      transitions from: :order_placed, to: :paid
    end

    event :ship do
      transitions from: :paid, to: :shipping
    end

    event :deliver do
      transitions from: :shipping, to: :shipped
    end

    event :return_good do
      transitions from: :shipped, to: :good_returned
    end

    event :cancel_order do
      transitions from: [:order_placed, :paid], to: :order_cancelled
    end
  end
end
```

### 訂單狀態

| State | 用途 |
|---|---|
| `order_placed` | 訂單成立 |
| `paid` | 已付款 |
| `shipping` | 出貨中 |
| `shipped` | 已出貨 |
| `order_cancelled` | 訂單取消 |
| `good_returned` | 商品退貨 |

### 狀態轉換流程

```text
order_placed（訂單成立）
│
├── make_payment
│   └── paid（已付款）
│       │
│       ├── ship
│       │   └── shipping（出貨中）
│       │       └── deliver
│       │           └── shipped（已出貨）
│       │               └── return_good
│       │                   └── good_returned（已退貨）
│       │
│       └── cancel_order
│           └── order_cancelled（已取消）
│
└── cancel_order
    └── order_cancelled（已取消）
```

### Event 與 State 的差別

- **State**：訂單目前處於什麼狀態，例如 `paid`。
- **Event**：觸發狀態變更的操作，例如 `make_payment`。
- **Transition**：限制 Event 可以從哪些狀態轉換到哪些狀態。

例如：

```ruby
event :ship do
  transitions from: :paid, to: :shipping
end
```

表示只有處於 `paid` 狀態的訂單，才能透過 `ship` 事件轉換成 `shipping`。

## 5. 整合付款流程

原始開發紀錄在 `make_payment` 事件加入 Callback：

```ruby
event :make_payment do
  transitions from: :order_placed, to: :paid

  after do
    pay!
  end
end
```

目的：將付款處理與狀態機事件整合，讓相關流程集中管理。

**注意：** `pay!` 的實際用途需要搭配原始 `Order` Model 確認。若涉及外部金流或其他資料庫更新，也需要考慮失敗處理與交易一致性。

## 6. Controller：統一透過 Event 操作

`app/controllers/account/orders_controller.rb`

原本：

```ruby
@order.pay!
```

修改為：

```ruby
@order.make_payment!
```

透過 AASM Event 執行狀態轉換，而不是直接修改 `aasm_state` 欄位。

### Event 呼叫方式

```ruby
order.make_payment   # 執行事件，不直接持久化狀態
order.make_payment!  # 執行事件並儲存狀態
```

實際使用時仍需處理不允許的狀態轉換及儲存失敗情況。

```bash
git add .
git commit -m "feat(order): add order state machine with AASM"
```

```bash
git add .
git commit -m "refactor(order): route payment flow through AASM events"
```

## 7. 驗證訂單狀態

重新操作訂單建立與付款流程後，開啟 Rails Console：

```bash
bin/rails console
```

查看最新訂單：

```ruby
order = Order.last
```

查看狀態欄位：

```ruby
order.aasm_state
```

付款成功後預期：

```text
"paid"
```

查看完整訂單資料：

```ruby
order.attributes
```

---

## Git Workflow

**Feature：** Order State Machine（AASM）

**目標：** 使用 AASM 管理訂單生命週期，並將付款流程整合至狀態機。

### 開發內容

- 安裝 AASM
- 建立 `aasm_state` 欄位
- 定義訂單狀態與轉換規則
- 整合付款 Callback
- Controller 改用 AASM Event
- 驗證訂單付款後的狀態

### Branch

`feat/order-state-machine`

### Commit History

```text
feat(order): add order state machine with AASM
refactor(order): route payment flow through AASM events
```
