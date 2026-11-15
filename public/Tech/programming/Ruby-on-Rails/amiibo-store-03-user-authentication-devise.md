2026 03 18 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 會員登入系統

## 目的

使用 **Devise** 建立會員註冊、登入、登出功能，並整合 Tailwind CSS，讓 Navbar 根據會員登入狀態顯示不同內容。

## 1. 建立 Git 分支

```bash
git checkout -b feat/auth-devise
```

## 2. 安裝 Devise

Devise 是 Rails 常用的會員驗證 Gem。

在 `Gemfile` 加入：

```ruby
gem "devise"
```

安裝依賴：

```bash
bundle install
```

產生 Devise 設定與 User model：

```bash
bin/rails generate devise:install
bin/rails generate devise User
bin/rails db:migrate
```

完成後，Devise 會提供會員註冊、登入與登出等功能。

```bash
git add .
git commit -m "feat(auth): install Devise and set up user authentication"
```

## 3. 客製化 Devise Views

Devise 預設提供會員驗證頁面。

如果需要自行修改 HTML / ERB，並套用 Tailwind CSS，可以產生 Views：

```bash
bin/rails generate devise:views
```

產生後可修改：

```text
app/views/devise/
├── registrations/  # 註冊頁面
├── sessions/       # 登入頁面
├── passwords/      # 密碼重設
└── shared/         # 共用內容
```

依照專案需求調整 Tailwind 樣式。

```bash
git add .
git commit -m "feat(auth): customize Devise views with Tailwind styling"
```

## 4. Navbar 顯示會員登入狀態

檔案：

`app/views/common/_navbar.html.erb`

使用 Devise 提供的 `current_user` 判斷會員是否登入。

```erb
<% if current_user %>
  <span>Hi, <%= current_user.email %></span>
<% else %>
  <%= link_to "註冊", new_user_registration_path %>
  <%= link_to "登入", new_user_session_path %>
<% end %>
```

### 登出功能

Devise 登出需要送出 `DELETE` 請求。

搭配 Rails Turbo：

```erb
<%= link_to "登出",
    destroy_user_session_path,
    data: { turbo_method: :delete },
    class: "text-red-500 hover:text-red-700" %>
```

### 會員 Dropdown Menu

登入後顯示會員選單，提供商品頁面與登出功能。

```erb
<% if current_user %>
  <div class="relative group">
    <button class="px-4 py-2 rounded-lg border">
      Hi, <%= current_user.email %>
    </button>

    <div class="absolute right-0 hidden group-hover:block bg-white shadow-lg rounded-lg">
      <%= link_to "登出", destroy_user_session_path,
          data: { turbo_method: :delete },
          class: "block px-4 py-2 text-red-500" %>
    </div>
  </div>
<% end %>
```

這裡使用 Tailwind 的 `group-hover`，讓滑鼠移入會員區塊時顯示選單。

```bash
git add .
git commit -m "feat(auth): update navbar for authentication state"
```

## 5. 功能驗證

啟動 Rails：

```bash
bin/dev
```

確認以下流程：

- 未登入：Navbar 顯示「註冊」、「登入」。
- 註冊會員：可以建立新帳號。
- 登入會員：Navbar 顯示會員 Email。
- 會員選單：可以展開 Dropdown。
- 登出會員：成功結束登入狀態，Navbar 恢復未登入畫面。

---

## Git Workflow

**Feature：** User Authentication（Devise）

**目標：** 建立會員驗證系統，整合 Tailwind CSS 與 Navbar。

### 開發內容

- 安裝 Devise 並建立 User model
- 完成會員註冊、登入、登出功能
- 客製化 Devise Views
- Navbar 根據登入狀態顯示不同內容
- 建立會員 Dropdown Menu

### Branch

`feat/auth-devise`

### Commit History

```text
feat(auth): install Devise and set up user authentication
feat(auth): customize Devise views with Tailwind styling
feat(auth): update navbar for authentication state
```
