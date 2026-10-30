2026 10 30 Created

---

# 免費 PostgreSQL 30 天到期，重建資料庫方法

## 重建 PostgreSQL (它是資料庫管理系統)

1. 刪除原本過期的 PostgreSQL（確認資料可透過 Seeds 重建）。
2. 建立新的 PostgreSQL（Region 要和 Rails 網站相同）。
3. 複製 `Internal Database URL`。
4. 到網站 `Environment → DATABASE_URL`，貼上新網址、儲存並重新部署。

## 同伺服器建立第二個 Database

> 用途：兩個 Rails 專案使用不同 Database，避免資料表、Migration 衝突。

1. 進入新建立的 PostgreSQL → `Connect → PSQL Command`。
   - PSQL Command：讓 Mac 終端機連線到 Render PostgreSQL 的指令。
2. 複製整串 PSQL Command，貼到終端機執行。
3. 成功連線後，輸入：

```sql
CREATE DATABASE bamu_production;
```

4. 成功會顯示：

```text
CREATE DATABASE
```

5. 輸入 `\q` 離開 PostgreSQL。

## 修改第二個網站的 DATABASE_URL

1. 第二個網站 → `Environment → DATABASE_URL`。
2. 點眼睛查看網址，把最後的 Database 名稱改成 `bamu_production`。
3. 其他部分不變，儲存並重新部署。

## 最終架構

```text
PostgreSQL（免費伺服器 × 1）
├── bamu_projects_db_v9fm → Amiibo Store
└── bamu_production        → Bamu Portfolio
```
