2026 10 07 Created
2026 11 02 Moved from Discord-peko
---

# Java

## 基本介紹

Java：1995 年由 Sun Microsystems 推出的程式語言，後由 Oracle 收購。常用於銀行、電商、大型公司的後端系統。

- Java（程式語言）：用來撰寫程式，常見於網站後端、企業管理系統。
- JVM（Java 虛擬機）：讓 Java 程式能在 Windows、macOS、Linux 等不同系統執行。
- Spring Boot（Web Framework）：提供開發網站後端所需的架構與常用功能。

應用範例：
- 銀行：帳戶、轉帳、交易紀錄。
- 電商：會員、訂單、庫存管理。
- 企業系統：員工、薪資、客戶資料管理。
- Android：手機 App 開發（目前也廣泛使用 Kotlin）。

## 核心特色

### 1. 跨平台（Write Once, Run Anywhere）
- Java 程式通常先編譯成 Bytecode（位元組碼）。
- 再透過 JVM（Java 虛擬機）執行。
- 不同作業系統只要有相容的 JVM，就能執行相同的 Java Bytecode。

```text
Java 程式碼
     ↓
Java Compiler（編譯器）
     ↓
Java Bytecode
     │
     ├── Windows → JVM → 執行
     ├── macOS   → JVM → 執行
     └── Linux   → JVM → 執行
```

### 2. 物件導向（OOP）
- 支援 Class、封裝、繼承、多型。
- 適合組織大型專案，方便維護與擴充。

### 3. 自動記憶體管理
- JVM 提供 Garbage Collection（垃圾回收）。
- 自動回收不再使用的受管理記憶體。
- 減少手動管理記憶體的負擔，但仍需注意資源釋放。

### 4. 成熟的企業生態系
- 擁有大量函式庫、框架與開發工具。
- Spring Boot 可簡化 Java 後端開發與部署。
- 常用於企業系統、Web API、微服務與雲端服務。
- 現代 Java 持續加入新語法與功能，改善開發效率。

## 常見開發工具

| 工具 | 用途 |
|---|---|
| JDK | Java 開發工具包，包含編譯器等工具 |
| JVM | 執行 Java Bytecode 的虛擬機 |
| Spring | Java 應用程式框架 |
| Spring Boot | 簡化 Spring 專案設定與啟動 |
| Maven / Gradle | 專案建置、依賴管理 |
| PostgreSQL / MySQL | 關聯式資料庫 |
| IntelliJ IDEA | 常見的 Java IDE |
| VS Code | 編輯器，搭配 Java 擴充功能 |

補充：
- Java：程式語言。
- JVM：執行環境的核心。
- JDK：開發 Java 所需的工具包。
- Spring：提供應用程式架構與常用功能。
- Spring Boot：建立在 Spring 生態系上，讓後端專案更容易設定、啟動與部署。

## 應用領域

```text
Java
│
├── 🌐 Web Backend（網頁後端）⭐
│   └── Spring / Spring Boot
│       ├── Web API
│       ├── 企業系統
│       └── Microservices（微服務）
│
├── 🏦 金融與企業系統 ⭐
│   ├── 銀行、證券、支付系統
│   ├── 電信、航空、物流系統
│   └── 大型企業內部系統
│
├── ☁️ Cloud / Cloud-Native
│   ├── 雲端後端服務
│   ├── 微服務架構
│   └── Docker / Kubernetes 部署
│
├── 📊 Big Data（大數據）
│   ├── Hadoop → 分散式資料處理
│   └── Apache Kafka → 事件串流平台
│
├── 📱 Android（行動應用程式）
│   ├── Java → 傳統 Android 開發
│   └── Kotlin → 現代 Android 常用語言
│
└── 🎮 Game Development（遊戲開發）
    └── Minecraft Java Edition
        → 以 Java 開發的知名遊戲
```

## 重點整理

- Java：成熟、跨平台、靜態型別的通用程式語言。
- JVM：讓 Java Bytecode 能在不同平台執行。
- Spring / Spring Boot：Java 企業後端常見框架與工具。
- 主要優勢：跨平台、成熟生態系、長期維護性。
- 常見領域：企業後端、金融、電信、雲端、大數據。
- 與 Rails 比較：Java / Spring 在大型企業後端市場應用廣泛；Rails 則強調慣例與快速開發。
