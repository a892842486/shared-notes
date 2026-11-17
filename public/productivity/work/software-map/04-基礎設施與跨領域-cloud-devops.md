2026 10 08 Created
2026 11 02 Moved from Discord-peko
---

# Cloud / DevOps

## 基本介紹

### Cloud（雲端運算）
- 簡單說，就是「租用別人的電腦與基礎設施」，依需求擴展。
- 透過 AWS、Google Cloud、Azure 等平台使用運算資源，不必自行購買與維護伺服器。
- 提供運算、網路、儲存、資安、自動擴展等服務。

範例：搶票網站流量暴增時，可透過預先設定的 Auto-scaling 自動增加資源，流量下降後再縮減。

### DevOps（Development + Operations）
- 把「開發軟體」與「維護運行環境」結合，透過自動化讓軟體更快、更穩定地上線。
- 強調開發與維運協作、持續交付及系統穩定。

常見技術：
- CI/CD：自動建置、測試、部署。
- IaC：透過程式碼管理基礎設施，例如 Terraform。
- Monitoring / Logging：監控系統狀態、記錄錯誤與事件。

### 兩者關係
- Cloud：提供軟體運行所需的資源。
- DevOps：改善軟體開發、部署與維護流程。
- 經常搭配使用，但 DevOps 不一定需要雲端。

## 應用架構

```text
Cloud / DevOps
│
├── ☁️ Cloud（雲端運算）
│   ├── AWS
│   ├── Google Cloud（GCP）
│   └── Microsoft Azure
│       → 雲端運算、儲存、網路等服務
│
├── 💻 Infrastructure（基礎設施）
│   ├── Linux → 作業系統
│   ├── Networking → 網路
│   └── Storage → 資料儲存
│
├── 📦 Container（容器）
│   ├── Docker → 建立、執行容器
│   └── Kubernetes → 容器編排與管理
│
├── 🚀 CI/CD（自動化交付）
│   └── GitHub Actions
│       → 自動建置、測試、部署
│
├── 🏗️ IaC（基礎設施即程式碼）
│   └── Terraform
│       → 使用程式碼管理基礎設施
│
├── 📊 Monitoring / Logging
│   ├── Monitoring → 系統監控
│   └── Logging → 日誌紀錄
│
└── 🖥️ 常搭配的程式語言
    ├── Bash → Shell 腳本、自動化
    ├── Python → 自動化、工具開發
    └── Go → 雲端、DevOps、網路服務
```
