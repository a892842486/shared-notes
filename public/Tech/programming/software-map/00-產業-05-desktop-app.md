2026 11 17 Created

---

# Desktop App 開發概況

> Desktop App（桌面應用程式）是執行於 Windows、macOS、Linux 等電腦作業系統的應用程式。

## 常見開發技術

```text
🖥️ Desktop App
│
├── 平台專屬技術
│   ├── Windows
│   │   ├── C# / .NET → WPF、WinForms、WinUI
│   │   └── C++ → Win32
│   │
│   └── macOS
│       ├── Swift → SwiftUI、AppKit
│       └── Objective-C → AppKit、既有專案
│
└── 跨平台框架
    ├── Electron → JavaScript / TypeScript
    ├── Flutter → Dart
    ├── Qt → C++、Python
    ├── GTK → C、Python 等
    └── .NET MAUI → C#
```

## 桌面應用程式主要工作

1. **GUI 使用者介面**：視窗、按鈕、選單、操作流程。
2. **本機檔案處理**：讀取、儲存、管理檔案。
3. **作業系統整合**：通知、剪貼簿、快捷鍵、系統匣。
4. **硬體與效能**：音訊、影像、GPU、記憶體。
5. **網路與資料同步**：API、帳號登入、雲端服務。

## 重點整理

- **平台專屬開發**：使用特定作業系統的技術，方便整合平台功能。
- **跨平台開發**：共用程式碼支援多個作業系統，實際支援範圍依框架而定。
- **Electron**：使用 Web 技術開發桌面應用程式。
- **Qt / GTK**：常見的桌面 GUI 開發工具。
- **Flutter / .NET MAUI**：也能開發桌面應用程式，但支援平台依框架而異。

**結論：** Windows 可認識 C# / .NET，macOS 可認識 Swift；跨平台則可依需求選擇 Electron、Qt、Flutter 等技術。Linux 桌面應用常見 Qt、GTK。
