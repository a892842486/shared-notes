---
2026-10-16 Created
2026-10-16 Updated
---

# GitHub 筆記更新流程


## 1. 上傳筆記至 GitHub

```powershell
git status
git add .
git commit -m "docs: update notes"
git push
```

## 2. 更新公開網站

```powershell
py -m mkdocs gh-deploy
```

## 3. Commit 訊息規範

| 類型 | 用途 | 範例 |
|---|---|---|
| `docs` | 新增或修改筆記 | `docs: update notes` |
| `docs` | 新增特定主題筆記 | `docs: add health notes` |
| `docs` | 修正錯字 | `docs: fix typos` |
| `chore` | 修改專案設定或維護事項 | `chore: update config` |





