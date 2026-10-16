2026-10-17 Created

---

# Linux 桌面環境與 Rice 整理

## Linux 桌面架構

| 層級 | 分類 | 主要功能 |
|---|---|---|
| 第一層 | Linux 作業系統 | 提供 Linux 核心、系統工具、套件管理等基礎。 |
| 第二層 | 完整桌面環境／視窗管理器 | 決定視窗如何排列、移動，以及桌面如何運作。 |
| 第三層 | 桌面小工具與介面 | 提供狀態列、啟動器、登出選單等介面。 |
| 第四層 | Rice（桌面配置） | 統一配色、字體、圖示、動畫、桌布、終端機主題與操作設定。 |

## 桌面環境組合比較

| 定位 | 作業系統 | 桌面環境／視窗管理器 | 桌面小工具／介面 | Rice／Dotfiles |
|---|---|---|---|---|
| 開箱即用 | Linux Mint | XFCE | Waybar、Rofi、Wlogout、Picom | — |
| 完整桌面 | CachyOS | KDE Plasma | — | — |
| 自由組裝 | CachyOS | Hyprland | Waybar、Rofi、Wlogout | 自行配置 |
| 套件整合 | CachyOS | Hyprland | Caelestia Dot | [bitzdots](https://github.com/bitzzdev/bitzdots)、[hyprland-glassmorphism](https://github.com/v-o-h-s/hyprland-glassmorphism) |
| 套件整合 | CachyOS | Niri | Noctalia | [hqhoangvuong/hyprland-cachyos-setup](https://github.com/hqhoangvuong/hyprland-cachyos-setup)、[ML4W OS](https://github.com/mylinuxforwork/dotfiles) |
| Rice 範例 | — | Hyprland | — | [end-4 / dots-hyprland](https://github.com/end-4/dots-hyprland) |

## 名詞備忘

- **Linux 發行版：** 例如 Linux Mint、CachyOS，提供作業系統基礎與套件管理。
- **桌面環境（DE）：** 例如 XFCE、KDE Plasma，通常整合多種桌面功能。
- **視窗管理器（WM）：** 負責視窗的排列、移動與管理，例如 Hyprland、Niri。
- **桌面小工具：** 例如 Waybar（狀態列）、Rofi（啟動器）、Wlogout（登出選單）。
- **Rice／Dotfiles：** 用設定檔與外觀配置，打造一致的桌面風格與操作體驗。