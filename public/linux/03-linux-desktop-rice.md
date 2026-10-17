2026-10-18 Updated

---

# Linux Desktop Rice

## Rice（桌面個人化配置）

Rice 指的是對桌面外觀與操作方式進行個人化調整的成果；Ricing 則是調整與美化桌面的過程。

可以直接使用桌面環境內建的功能，也可以套用別人整合好的配置，或自行安裝與設定各種桌面元件。

## Rice 分類

| 方式 | 說明 |
|---|---|
| **開箱即用** | 使用桌面環境內建的介面與功能，例如 Xfce、Cinnamon、KDE Plasma，安裝後即可使用，不需要額外配置。 |
| **整合式（Dotfiles）** | 使用整合好的 Dotfiles 套件，快速取得預先配置的桌面外觀與操作體驗。 |
| **半自訂（Desktop Shell）** | 選擇適合的 Desktop Shell，搭配需要的桌面工具，再依需求調整外觀與功能。 |
| **全手工** | 自行安裝與設定桌面所需的工具、主題與操作方式，逐步打造自己的桌面體驗。 |

## 整合式配置（Dotfiles）

這類專案通常會整合多種桌面工具、設定檔、主題與快捷鍵，讓使用者快速建立一套完整的桌面體驗。

| 名稱 | 特色 | 搭配環境 |
|---|---|---|
| [end-4 / illogical-impulse](https://github.com/end-4/dots-hyprland) | 壁紙自動配色、流暢動畫、即時視窗預覽，並整合搜尋與 AI 功能。 | Hyprland |
| [ML4W Dotfiles](https://github.com/mylinuxforwork/dotfiles) | 提供多種主題與外觀設定，可透過圖形化工具調整面板、模糊效果、動畫與快捷鍵。 | 主要搭配 Hyprland |
| [Caelestia Dot](https://github.com/caelestia-dots/caelestia) | 具有流暢動畫、動態配色與變形式桌面介面，整體風格現代且具有科技感。 | Hyprland |
| [bitzdots](https://github.com/bitzzdev/bitzdots) | 著重低資源消耗與自動配色，官方宣稱整套桌面閒置時約使用 250–300 MB RAM。 | Hyprland |
| [hyprland-glassmorphism](https://github.com/v-o-h-s/hyprland-glassmorphism) | 採用半透明磨砂玻璃、模糊背景、白色細邊框與黑白灰配色。 | Hyprland |

> Dotfiles 原本是指保存程式與系統設定的檔案；這裡的「桌面配置套件」是指將多份設定與相關工具整合起來的專案。

## 半自訂配置（Desktop Shell）

Desktop Shell 負責提供使用者與桌面互動的介面，例如面板、選單、小工具、通知、控制中心及其他桌面功能。

使用 Shell 可以先取得一套整合好的桌面介面，再依照自己的需求調整外觀、功能與其他桌面元件。

| 名稱 | 特色 | 搭配合成器 |
|---|---|---|
| [Caelestia Shell](https://github.com/caelestia-dots/shell) | 提供流暢動畫、動態配色與可變化的桌面介面，與 Caelestia Dot 深度整合。 | 主要搭配 Hyprland |
| [Noctalia Shell](https://github.com/noctalia-dev/noctalia) | 整合面板、應用程式啟動器、通知、控制中心與桌面小工具，支援動態配色。 | Niri、Hyprland、Sway 等 |
| [DankMaterialShell（DMS）](https://github.com/AvengeMedia/DankMaterialShell) | 提供完整桌面介面、控制中心、動態主題與應用程式啟動器，整合多種桌面服務。 | Niri、Hyprland、Sway 等 |

### 特殊案例

- [Caelestia KDE](https://github.com/ladybug-me/caelestia-kde)：將 Caelestia Shell 搭配 KDE Plasma 使用，屬於不同於原本 Hyprland 配置的整合方式。

## 全手工配置（桌面元件）

全手工配置是自行選擇、安裝與設定各種桌面工具，再依照自己的需求組合成完整的桌面體驗。

### 桌面互動工具

| 功能 | 工具 | 主要用途 |
|---|---|---|
| 狀態列 | [Waybar](https://github.com/Alexays/Waybar) | 顯示時鐘、工作區、音量、CPU 等系統資訊。 |
| Waybar 主題 | [atif-1402](https://github.com/atif-1402/minimal-waybar-themes)、[miniMinn24](https://github.com/miniMinn24/minimal-waybar)、[soaddevgit](https://github.com/soaddevgit/WaybarTheme) | 提供可參考或套用的 Waybar 外觀配置。 |
| 狀態列 | [Tint2](https://github.com/nick87720z/tint2)、[Polybar](https://github.com/polybar/polybar) | 提供面板與狀態列功能，常見於 X11 桌面配置。 |
| 應用程式啟動器 | [Rofi](https://github.com/davatorium/rofi) | 搜尋程式、啟動應用程式及建立自訂選單。 |
| 電源控制選單 | [Wlogout](https://github.com/ArtsyMacaw/wlogout) | 提供關機、重新啟動、登出等選項。 |
| 視窗平鋪 | [Cortile](https://github.com/leukipp/cortile) | 在傳統桌面環境中提供自動平鋪視窗功能，主要用於 X11。 |
| Dock | [Plank Reloaded](https://github.com/zquestz/plank-reloaded) | 提供可停靠在螢幕邊緣的應用程式啟動列。 |
| 視窗合成器 | [Picom](https://github.com/yshui/picom) | 為視窗加入透明度、陰影等視覺效果，主要用於 X11。 |
| 視窗合成器 | [xcompmgr-simple](https://github.com/jmanc3/xcompmgr-simple) | 提供 X11 視窗合成與基本視覺效果。 |
| 通知系統 | [Mako](https://github.com/eugeniosegala/MAKO)、[SwayNotificationCenter](https://github.com/ErikReider/SwayNotificationCenter) | 顯示與管理桌面通知。 |
| 鎖定畫面 | [Hyprlock](https://github.com/hyprwm/hyprlock)、[Swaylock](https://github.com/swaywm/swaylock) | 鎖定桌面，避免他人直接操作電腦。 |
| 螢幕截圖 | [Grim](https://github.com/emersion/grim)、[Slurp](https://github.com/emersion/slurp) | 擷取 Wayland 桌面畫面或選取區域。 |

實際需要安裝哪些工具，取決於使用的桌面環境、視窗管理器或 Wayland 合成器，以及個人需求。X11 與 Wayland 的工具並不完全通用，安裝前應確認相容性。

### 外觀配置（Visual）

| 項目 | 工具／資源 | 說明 |
|---|---|---|
| GTK 主題 | [Nordic](https://github.com/EliverLara/Nordic)、[Orchis](https://github.com/vinceliuice/orchis-theme)、[Colloid](https://github.com/vinceliuice/Colloid-gtk-theme) | 調整 GTK 應用程式的配色與介面元件外觀。 |
| 英文字型 | [JetBrains Mono](https://www.jetbrains.com/lp/mono/#how-to-install) | 適合程式碼與終端機的等寬字型。 |
| 中文字型 | [思源黑體](https://github.com/adobe-fonts/source-han-sans/blob/master/README-TW.md) | 提供中文介面與文字顯示所需的字型。 |
| 滑鼠游標 | [Bibata Cursor](https://github.com/ful1e5/Bibata_Cursor)、[Phinger Cursors](https://github.com/phisch/phinger-cursors) | 調整滑鼠游標的外觀。 |
| 應用程式圖示 | [Papirus Icon](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme)、[Tela Circle Icon](https://github.com/vinceliuice/Tela-circle-icon-theme) | 統一應用程式圖示的風格。 |
| 桌布 | [Wallhaven](https://wallhaven.cc/) | 搜尋與取得桌面背景圖片。 |

### 操作配置（Interaction）

| 項目 | 說明 |
|---|---|
| 快捷鍵 | 設定開啟程式、切換視窗與系統操作的按鍵。 |
| 視窗排列 | 設定平鋪、浮動與視窗尺寸。 |
| 工作區 | 設定工作區切換與視窗分配方式。 |
| 面板行為 | 設定面板自動隱藏、位置與顯示方式。 |
| 滑鼠操作 | 設定拖曳視窗、手勢與其他互動方式。 |
