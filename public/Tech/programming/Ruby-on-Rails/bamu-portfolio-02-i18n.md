2026 08 04 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails I18n 多語系設定與語言切換

## 目的

讓 Portfolio 網站支援英文與繁體中文，並透過網址中的 `locale` 參數決定目前語言。

開發內容：

1. 設定網站支援的語言
2. 建立多語系 Routes
3. 在 Controller 設定當前語言
4. 建立語言切換按鈕
5. 整合 Navbar
6. 建立翻譯檔並驗證

## 1. 建立 Git 分支

```bash
git checkout -b feat/i18n-setup
```

## 2. 設定網站支援的語言

`config/application.rb`

```ruby
config.load_defaults 8.1
config.i18n.default_locale = :en
config.i18n.available_locales = [:en, :"zh-TW"]
```

- `default_locale`：網站預設語言為英文。
- `available_locales`：允許英文與繁體中文。
- `:"zh-TW"`：Ruby Symbol，因為名稱包含 `-`，所以使用引號。

> `config.load_defaults 8.1` 是原始專案的設定，其他專案應保留自己實際使用的 Rails 版本。

## 3. 建立多語系 Routes

`config/routes.rb`

```ruby
Rails.application.routes.draw do
  scope "(:locale)", locale: /en|zh-TW/ do
    devise_for :users

    root "welcome#index"
    get "welcome/index"
  end

  get "up" => "rails/health#show", as: :rails_health_check
end
```

### scope "(:locale)"

`(:locale)` 代表可選擇的網址參數。

例如：

```text
/              → 預設英文
/en            → 英文
/zh-TW         → 繁體中文
/en/welcome/index
/zh-TW/welcome/index
```

`locale: /en|zh-TW/` 限制路由接受的語言代碼。

`devise_for :users` 放在 Scope 內，讓登入、註冊等 Devise 路由也支援語言參數。

`/up` 健康檢查路由放在 Scope 外，不需要語言前綴。

> 原始範例只列出部分 Routes。其他需要支援多語系的前台路由，也應放進相同的 Scope。

## 4. Controller：設定當前語言

`app/controllers/application_controller.rb`

```ruby
before_action :set_locale

def default_url_options
  { locale: I18n.locale }
end

private

def set_locale
  I18n.locale =
    if I18n.available_locales.map(&:to_s).include?(params[:locale])
      params[:locale].to_sym
    else
      I18n.default_locale
    end
end
```

### before_action :set_locale

每次執行 Controller Action 前，先根據網址參數設定語言。

```text
/zh-TW
   ↓
params[:locale] = "zh-TW"
   ↓
set_locale
   ↓
I18n.locale = :"zh-TW"
```

如果網址沒有提供有效語言，就使用 `I18n.default_locale`。

### default_url_options

```ruby
def default_url_options
  { locale: I18n.locale }
end
```

讓 Rails URL Helper 預設帶入目前語言。

例如：

```erb
<%= link_to "Home", root_path %>
```

在繁體中文環境下，會使用 `zh-TW` 作為路由的 Locale。

> 這個設定影響 Rails URL Helper 產生的網址，不會自動修改手動寫死的網址。

## 5. 建立語言切換按鈕

建立 Partial：

```bash
touch app/views/shared/_locale_switcher.html.erb
```

`app/views/shared/_locale_switcher.html.erb`

```erb
<div data-controller="dropdown" class="relative inline-block">
  <!-- Button -->
  <button
    type="button"
    data-action="click->dropdown#toggle"
    class="inline-flex items-center gap-2 rounded-lg bg-gray-100 px-3 py-2 text-sm font-medium text-gray-800 hover:bg-gray-200">

    <span>
      <%= I18n.locale == :en ? "EN" : "中文" %>
    </span>

    <svg class="h-4 w-4 transition-transform"
         data-dropdown-target="icon"
         fill="none"
         stroke="currentColor"
         viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M19 9l-7 7-7-7" />
    </svg>
  </button>

  <!-- Dropdown -->
  <div
    data-dropdown-target="menu"
    class="absolute right-0 z-50 mt-2 w-32 rounded-lg border bg-white shadow-lg hidden">

    <%= link_to url_for(locale: :en, only_path: true),
        class: "block px-4 py-2 text-sm hover:bg-gray-100 #{'bg-gray-50 font-bold' if I18n.locale == :en}" do %>
      English
    <% end %>

    <%= link_to url_for(locale: :"zh-TW", only_path: true),
        class: "block px-4 py-2 text-sm hover:bg-gray-100 #{'bg-gray-50 font-bold' if I18n.locale == :"zh-TW"}" do %>
      中文
    <% end %>
  </div>
</div>
```

### Stimulus Dropdown

| 屬性 | 用途 |
|---|---|
| `data-controller="dropdown"` | 連接 Stimulus Controller |
| `data-action="click->dropdown#toggle"` | 點擊時執行 `toggle` |
| `data-dropdown-target="menu"` | 指定下拉選單 |
| `data-dropdown-target="icon"` | 指定箭頭圖示 |

這裡使用既有的 `dropdown_controller.js` 控制下拉選單開關。

> 原始紀錄沒有提供 `dropdown_controller.js` 的內容，因此本篇不額外建立或推測其實作。

### url_for 切換語言

```erb
<%= link_to url_for(locale: :"zh-TW", only_path: true) do %>
  中文
<% end %>
```

- `locale`：指定目標語言。
- `only_path: true`：產生相對路徑，而非完整網址。
- `url_for`：依照目前路由資訊產生網址，適合在同一頁面切換語言。

例如：

```text
/en/welcome/index
        ↓
/zh-TW/welcome/index
```

> 如果目前頁面的路由不支援 Locale，或有特殊路由參數，仍需要個別確認產生的網址是否正確。

## 6. 整合 Navbar

`app/views/shared/_navbar.html.erb`

```erb
<div class="flex items-center gap-3">
  <%= render "shared/locale_switcher" %>

  <% if user_signed_in? %>
    <%= render "shared/navbar_user" %>
  <% else %>
    <%= render "shared/navbar_guest" %>
  <% end %>
</div>
```

將語言切換器加入 Navbar。

- `locale_switcher`：語言切換按鈕。
- `navbar_user`：已登入使用者的導覽列。
- `navbar_guest`：訪客的導覽列。

## 7. 建立翻譯檔並驗證

### View

`app/views/welcome/index.html.erb`

```erb
<p>Current locale: <%= t(".test") %></p>
```

`t(".test")` 使用 Rails I18n 的相對翻譯 Key。

在 `welcome/index` 頁面中，對應：

```text
welcome.index.test
```

### 英文翻譯

建立：

```bash
touch config/locales/en.yml
```

`config/locales/en.yml`

```yaml
en:
  welcome:
    index:
      test: "en test"
```

### 繁體中文翻譯

建立：

```bash
touch config/locales/zh-TW.yml
```

`config/locales/zh-TW.yml`

```yaml
zh-TW:
  welcome:
    index:
      test: "zh-TW 測試"
```

### 驗證結果

| 網址 | 預期顯示 |
|---|---|
| `/` | Current locale: en test |
| `/en` | Current locale: en test |
| `/zh-TW` | Current locale: zh-TW 測試 |

再測試 Navbar：

1. 英文切換至繁體中文。
2. 繁體中文切換回英文。
3. 切換後仍停留在相同頁面。
4. 點擊網站其他連結，確認語言參數有保留。
5. 測試 Devise 登入、註冊路由是否正常。

## 8. Git Commit

```bash
git status

git add config/application.rb config/routes.rb \
        app/controllers/application_controller.rb \
        app/views/shared/_navbar.html.erb \
        app/views/shared/_locale_switcher.html.erb \
        app/views/welcome/index.html.erb \
        config/locales/en.yml \
        config/locales/zh-TW.yml

git commit -m "feat(i18n): add locale routing, controller support, and navbar language switcher"
```

---

## Git Workflow

**Feature：** I18n Setup（多語系設定）

**目標：** 讓 Portfolio 支援英文與繁體中文，並可透過 Navbar 切換語言。

### 開發內容

- 設定 Rails I18n 支援語言
- 使用 Route Scope 加入 Locale
- 透過 `before_action` 設定當前語言
- 使用 `default_url_options` 保留 Locale
- 建立 Stimulus 語言切換下拉選單
- 整合 Navbar
- 建立 YAML 翻譯檔
- 驗證語言切換功能

### Branch

`feat/i18n-setup`

### Commit History

```text
feat(i18n): add locale routing, controller support, and navbar language switcher
```
