2026-10-17 Created

---

# Linux 桌面元件、Shell 與 Rice

## 桌面配置套件（Dotfiles）

這類專案通常會整合多種桌面工具、設定檔、主題與快捷鍵，讓使用者快速建立一套完整的桌面體驗。

| 名稱 | 特色 | 搭配環境 |
|---|---|---|
| Caelestia Dot | 整套桌面配置，包含桌面介面與相關設定。 | Hyprland |
| illogical-impulse | 整套桌面配置，著重視覺效果與桌面互動。 | Hyprland |
| end-4 | 整套桌面配置，具有豐富的桌面介面與客製化效果。 | Hyprland |
| ML4W Dotfiles | 整合式桌面配置，提供安裝與設定工具。 | 主要搭配 Hyprland |

> Dotfiles 原本是指保存程式與系統設定的檔案；這裡的「桌面配置套件」則是指將多份設定與相關工具整合起來的專案。

## 桌面 Shell

桌面 Shell 負責提供使用者與桌面互動的介面，例如面板、選單、小工具及其他桌面功能。

| 名稱 | 特色 | 搭配環境 |
|---|---|---|
| Caelestia Shell | 提供桌面互動介面與整合式桌面功能。 | 主要搭配 Hyprland |

### 特殊案例

- [Caelestia KDE](https://github.com/ladybug-me/caelestia-kde)：將 Caelestia Shell 搭配 KDE Plasma 使用，屬於不同於原本 Hyprland 配置的整合方式。

## 桌面元件（Desktop Components）

這些工具通常各自負責一項功能，可以依照桌面環境與個人需求選擇安裝。

| 功能 | 工具 | 主要用途 |
|---|---|---|
| 狀態列 | [Waybar](https://github.com/Alexays/Waybar) | 顯示時鐘、工作區、音量、CPU 等系統資訊。 |
| 應用程式啟動器 | [Rofi](https://github.com/davatorium/rofi) | 搜尋程式、啟動應用程式及建立自訂選單。 |
| 視窗合成器 | [Picom](https://github.com/yshui/picom) | 為視窗加入透明度、陰影等視覺效果，主要用於 X11。 |
| 電源控制選單 | [Wlogout](https://github.com/ArtsyMacaw/wlogout) | 提供關機、重新啟動、登出等選項。 |

## Rice（桌面個人化配置）

Rice 指的是對桌面外觀與操作方式進行個人化調整的成果；Ricing 則是調整與美化桌面的過程。

### 外觀配置（Visual）

| 項目 | 說明 |
|---|---|
| GTK 主題 | 調整應用程式的介面風格。 |
| 視窗裝飾 | 調整視窗邊框、標題列等外觀。 |
| App Icon | 統一應用程式圖示風格。 |
| 游標 | 調整滑鼠游標的外觀。 |
| 字型 | 英文:JetBrains Mono、中文:思源黑體 |
| 桌布 | 設定桌面背景。 |
| 視覺效果 | 背景模糊、透明度、陰影、動畫等。 |

### 操作配置（Interaction）

| 項目 | 說明 |
|---|---|
| 快捷鍵 | 設定開啟程式、切換視窗與系統操作的按鍵。 |
| 視窗排列 | 設定平鋪、浮動與視窗尺寸。 |
| 工作區 | 設定工作區切換與視窗分配方式。 |
| 面板行為 | 設定面板自動隱藏、位置與顯示方式。 |
| 滑鼠操作 | 設定拖曳視窗、手勢與其他互動方式。 |

### Dotfiles 與 GitHub Rice Repo

- **Dotfiles：** 保存桌面、終端機、編輯器及其他程式的設定檔。
- **GitHub Rice Repo：** 他人公開分享的桌面配置、設定檔、安裝腳本與主題資源。
- **自行配置：** 自己挑選桌面元件、外觀主題與操作方式，逐步建立個人化桌面。
- **套用現成配置：** 使用整合式 Dotfiles 專案，減少逐一安裝與設定的工作。

## 一般應用程式（Applications）

一般應用程式負責日常工作，不屬於桌面元件或 Rice 本身。它們可以搭配任何適合的桌面環境使用。

| 類別 | 例子 |
|---|---|
| 程式編輯器 | VS Code |
| 終端機 | Kitty、Alacritty |
| Shell | Zsh、Fish |
| 文件編輯 | ONLYOFFICE |
| 瀏覽器 | 依需求選擇 |

> 桌面配置可以包含應用程式的安裝與設定，但應用程式本身仍是獨立的軟體類別。