2026 11 07 Created

---

# A 功能完成
git add .
git commit -m "feat: add feature A"
git push -u origin feat/A

# GitHub 建立 PR 並 Merge

# 同步主分支
git switch master
git pull origin master

# 建立 B 功能分支
git switch -c feat/B