2026 03 16 Created
2026 11 14 Moved from peko-discord
---

# macOS Ruby on Rails 開發環境建置（RVM → rbenv）

## 目的

原本的 Ruby / Rails / Node 開發環境已使用約 7～8 年，Ruby 版本由 RVM 管理。

本次重新建立開發環境，改用 **rbenv** 管理 Ruby 版本，避免舊環境衝突。

## 環境架構與安裝順序

為了更新開發環境並避免新舊版本衝突，本次依照以下順序重建：

```text
macOS（作業系統）
│
├── [1] 移除 RVM（舊的 Ruby 版本管理工具）
│
├── 開發工具
│   ├── [2] Xcode Command Line Tools（編譯工具）
│   │
│   └── [3] Homebrew（macOS 套件管理器）
│       │
│       ├── [4] rbenv（Ruby 版本管理工具）
│       │   └── [5] Ruby（程式語言）
│       │       ├── RubyGems / gem（Ruby 套件管理）
│       │       ├── [6] Bundler（專案 gem 依賴管理）
│       │       └── [7] Rails（Web 應用框架）
│       │
│       └── [8] Node.js（JavaScript 執行環境）
│           └── npm（JavaScript 套件管理器）
│
└── [9] 驗證 Ruby / Rails 版本與執行路徑
```

> [1]～[9] 為本次實際操作順序，樹狀結構則用來表示各工具之間的關係。

## 1. 移除 RVM

RVM 與 rbenv 都是 Ruby 版本管理工具，改用 rbenv 前先移除舊的 RVM。

確認目前 Ruby / Rails 來源：

```bash
which ruby
which rails
```

如果出現 `.rvm` 路徑，代表仍使用 RVM。

移除 RVM：

```bash
rvm implode
rm -rf ~/.rvm
```

檢查 Shell 設定：

```bash
nano ~/.zshrc
```

移除殘留的 RVM 設定，例如：

```bash
source ~/.rvm/scripts/rvm
export PATH="$PATH:$HOME/.rvm/bin"
```

重新載入：

```bash
source ~/.zshrc
```

再次執行 `which ruby`、`which rails`，確認不再指向 `.rvm`。

## 2. 安裝 Xcode Command Line Tools

提供 macOS 編譯 Ruby 等工具所需的編譯環境。

```bash
xcode-select --install
```

## 3. 安裝 Homebrew

macOS 套件管理器。

確認是否已安裝：

```bash
brew -v
```

尚未安裝則執行：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

更新套件資訊：

```bash
brew update
```

## 4. 安裝 rbenv

```bash
brew install rbenv ruby-build
```

加入 zsh 設定：

```bash
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

確認版本：

```bash
rbenv -v
```

## 5. 安裝 Ruby

本次使用 Ruby `3.3.10`。

```bash
rbenv install 3.3.10
rbenv global 3.3.10
```

確認版本：

```bash
ruby -v
```

## 6. 安裝 Bundler

Bundler 用來管理 Rails 專案使用的 gems。

```bash
gem install bundler
bundler -v
```

## 7. 安裝 Rails

```bash
gem install rails
rails -v
```

## 8. 安裝 Node.js

```bash
brew install node
```

確認版本：

```bash
node -v
npm -v
```

如果 Homebrew 出現 Node 連結衝突，可視錯誤訊息檢查是否需要重新連結：

```bash
brew link --overwrite node
```

## 9. 驗證開發環境

```bash
which ruby
which rails
ruby -v
rails -v
```

確認 Ruby / Rails 指向 rbenv，例如：

```text
~/.rbenv/shims/ruby
~/.rbenv/shims/rails
```

而不是舊的 `.rvm` 路徑。

## 補充：舊 Rails 專案

rbenv 可以管理多個 Ruby 版本。

例如舊專案需要 Ruby `2.6.6`：

```bash
rbenv install 2.6.6
rbenv versions
```

進入舊專案資料夾，指定專案使用的 Ruby：

```bash
rbenv local 2.6.6
```

再依照專案需求安裝相容的 Bundler 與 gems：

```bash
bundle install
```

注意：舊版 Ruby 可能與新版 macOS、編譯工具不相容，不能保證安裝成功或舊專案能直接執行。

---

## 補充：Ruby / gem / Bundler 的關係

```text
Ruby（程式語言）
│
└── RubyGems（Ruby 套件管理系統）
    │
    ├── gem（Ruby 套件）
    │   ├── Rails（Web 框架）
    │   ├── Devise（會員登入 / 註冊）
    │   ├── Puma（Web 伺服器）
    │   └── pg（PostgreSQL 資料庫驅動）
    │
    └── Bundler（專案依賴管理工具）
        ├── Gemfile（宣告專案需要的 gems）
        ├── Gemfile.lock（鎖定實際使用的版本）
        └── bundle install（安裝專案依賴）
```

### 簡單結論

- **RubyGems**：Ruby 的套件管理系統，使用 `gem` 指令安裝套件。
- **gem**：Ruby 套件，例如 Rails、Devise、Puma。
- **Bundler**：管理每個 Rails 專案需要哪些 gems，以及使用的版本。
- **Gemfile**：宣告專案需要哪些 gems。
- **Gemfile.lock**：記錄實際解析出的套件版本，讓不同環境使用一致的依賴組合。

## 常用指令

| 指令 | 用途 |
|---|---|
| `rbenv versions` | 查看已安裝的 Ruby 版本 |
| `rbenv global 3.3.10` | 設定全域 Ruby 版本 |
| `rbenv local 3.3.10` | 設定目前專案的 Ruby 版本 |
| `ruby -v` | 查看 Ruby 版本 |
| `rails -v` | 查看 Rails 版本 |
| `which ruby` | 確認 Ruby 執行檔來源 |
| `gem install rails` | 安裝 Rails gem |
| `gem list` | 查看已安裝的 gems |
| `bundle install` | 依照 Gemfile 安裝專案依賴 |
| `bundle exec rails s` | 使用專案指定的 gems 啟動 Rails |