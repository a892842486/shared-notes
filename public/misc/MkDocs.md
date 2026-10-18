2026 10 18 Created

---
# MkDocs

## 一、MkDocs 是什麼？

MkDocs 是一套使用 Python 開發的靜態網站產生器（Static Site Generator，SSG），主要用途是把 Markdown 文件轉換成可以透過瀏覽器閱讀的網站。

適合用來建立技術文件、學習筆記、操作指南和個人知識庫。

可以把它想成一位自動排版的編輯助理：

- **Markdown：** 我寫的筆記。
- **MkDocs：** 建置工具，讀取文件與設定，產生靜態網站。
- **Git：** 版本控制工具，保存原始碼的變更歷史，並透過 `push` 同步到遠端。
- **GitHub Pages：** 發布服務，讓訪客透過網址瀏覽網站。

## 二、優點與限制

| 面向 | 說明 |
|---|---|
| 學習成本 | 低，會寫 Markdown 就能開始。 |
| 外觀客製化 | 可透過主題、CSS、JavaScript 與外掛擴充。 |
| 部署 | 可部署到 GitHub Pages 等靜態網站主機。 |
| 維護 | 一般文件網站不需要自行維護資料庫或後端伺服器。 |
| 限制 | 不適合直接處理需要後端資料庫、使用者登入、權限管理或即時資料更新的應用程式。 |

## 三、操作方式

### 1. 本機預覽

```powershell
py -m mkdocs serve
```

### 2. 檢查變更

```powershell
git status
```

### 3. 保存原始碼版本

```powershell
git add <要提交的檔案或資料夾>
git commit -m "docs: update shared notes"
git push
```

### 4. 發布網站

```powershell
py -m mkdocs gh-deploy
```

注意：`git add` 的路徑要依照實際修改的檔案調整，若有修改 CSS 或其他設定，也要一併納入。