2026 05 10 Created
2026 11 15 Moved from peko-discord
---

# macOS Ruby on Rails 初始化專案

## 目的

建立 Rails 8 專案基底，完成 Tailwind CSS 整合，並連接 GitHub Repository。

本次開發環境：

- Ruby 3.3.10
- Rails 8.1.2
- Tailwind CSS
- Git / GitHub

## 1. 建立 Rails 專案

建立專案並直接整合 Tailwind CSS：

```bash
rails new jdstore -c tailwind
cd jdstore
```

安裝專案依賴（若建立時已自動安裝完成，可略過）：

```bash
bundle install
```

使用 VSCode 開啟專案：

```bash
code .
```

啟動開發伺服器：

```bash
bin/dev
```

## 2. 初始化 Git

Rails 新建專案通常已經自動執行 Git 初始化。

確認狀態：

```bash
git status
```

若尚未初始化：

```bash
git init
```

確認主要分支為 `main`：

```bash
git branch -M main
```

## 3. Tailwind CSS 排錯

使用 `rails new -c tailwind` 建立專案時，正常情況已完成 Tailwind 整合。

若未安裝成功，可確認 Gemfile 是否包含 `tailwindcss-rails`。

必要時重新安裝：

```bash
bundle add tailwindcss-rails
bin/rails tailwindcss:install
bin/dev
```

> 此段屬於排錯備用流程，不需要每次建立專案都執行。

## 4. 確認版本與首次 Commit

```bash
ruby -v
rails -v
```

本次使用：

```text
Ruby 3.3.10
Rails 8.1.2
```

確認 Git 變更後提交：

```bash
git status
git add .
git commit -m "chore(init): bootstrap Rails 8 project with Tailwind"
```

## 5. 建立 GitHub Repository

1. 在 GitHub 建立新的 Repository。
2. 連接本機 Git Repository 與 GitHub。
3. 推送 `main` 分支。

```bash
git remote add origin git@github.com:你的帳號/專案名稱.git
git push -u origin main
```

## 6. 更新 Gem 依賴（視情況）

檢查依賴是否已安裝：

```bash
bundle check
```

安裝 Gemfile 指定的依賴：

```bash
bundle install
```

若需要主動更新依賴版本：

```bash
bundle update
```

檢查更新結果：

```bash
git status
git diff Gemfile.lock
```

如果 `Gemfile.lock` 有變動，確認更新內容後提交：

```bash
git add Gemfile.lock
git commit -m "chore(deps): update locked gem dependencies"
```

若沒有變動，就不需要 Commit。

---

## Git Workflow

**Feature：** Project Initialization

**目標：** 建立 Rails 8 + Tailwind CSS 專案，並完成 GitHub 初始化。

### 開發內容

- 建立 Rails 8 專案
- 整合 Tailwind CSS
- 初始化 Git / main 分支
- 確認 Ruby / Rails 版本
- 連接 GitHub Repository
- 視情況更新 Gem 依賴

### Branch

`main`

### Commit History

```text
chore(init): bootstrap Rails 8 project with Tailwind
chore(deps): update locked gem dependencies
```
