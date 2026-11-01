2026-10-17 Created

---

# Linux 桌面環境與 Rice 整理

## Linux 桌面架構

| 層級 | 分類 | 主要功能 |
|---|---|---|
| 第一層 | Linux 發行版（Distributions） | 提供完整的作業系統，包含 Linux 核心、系統工具與套件管理等。例如 Linux Mint、CachyOS。|
| 第二層 | 桌面環境／視窗管理器／Wayland 合成器（DE／WM／Compositor） | 提供圖形化桌面介面，管理視窗排列與桌面操作。 |
| 第三層 | 桌面個人化（Desktop Rice） | 自訂桌面外觀與操作方式，例如配色、字體、圖示、桌布、動畫與快捷鍵。 |
| 第四層 | 應用程式（Applications） | 提供日常使用與工作所需的軟體，例如瀏覽器、終端機、程式編輯器與文件工具。 |

### 補充觀念

- **桌面環境（Desktop Environment, DE）：** 通常整合視窗管理、面板、設定工具等桌面功能，例如 GNOME、KDE Plasma、Cinnamon。
- **視窗管理器（Window Manager, WM）：** 負責視窗的排列、移動與管理，例如 i3、Openbox。
- **Wayland 合成器（Wayland Compositor）：** 負責 Wayland 桌面的視窗管理、畫面合成與輸入處理，例如 Hyprland、Niri、Sway。
- **X11：** 傳統的 Linux 圖形顯示系統，視窗管理與畫面特效可以交由不同程式處理。
- **Wayland：** 較新的圖形顯示協定，通常由合成器負責視窗管理、畫面合成及與顯示系統的互動。

## 桌面環境組合比較

| 定位 | 作業系統 | 桌面環境／視窗管理器／Wayland 合成器 | Rice（桌面個人化） | 應用程式 |
|---|---|---|---|---|
| 開箱即用 | Linux Mint | Cinnamon／Xfce | — | — |
| 開箱即用 | CachyOS | KDE Plasma | — | — |
| 自由組裝 | CachyOS | Hyprland | 全手工配置：桌布、字型、圖示、游標、GTK 主題、視窗裝飾、Waybar、Rofi、Wlogout 等 | — |
| 套件整合 | CachyOS | Hyprland | 直接用專家設定好的風格：[end-4 / dots-hyprland](https://github.com/end-4/dots-hyprland)、[ML4W Dotfiles](https://github.com/mylinuxforwork/dotfiles)、[Caelestia Dot](https://github.com/caelestia-dots/caelestia)、[bitzdots](https://github.com/bitzzdev/bitzdots)、[hyprland-glassmorphism](https://github.com/v-o-h-s/hyprland-glassmorphism) | — |
| 套件整合 | CachyOS | Niri | [Noctalia Shell](https://github.com/noctalia-dev/noctalia) | — |

### 定位區別

- **開箱即用：** 安裝好桌面環境即可使用，也可使用內建主題更改外觀，或安裝桌面 Shell、Dotfiles 來自訂桌面介面與操作方式。
- **自由組裝：** 自行安裝與設定 Waybar、Rofi、Wlogout 等工具，組合出自己的桌面。
- **套件整合：** 從整合好的 Dotfiles 中擇一安裝，快速取得預先配置的桌面外觀與操作體驗。

### Rice 分類

- **開箱即用：** 使用桌面環境內建的介面與功能，例如 Xfce、Cinnamon、KDE Plasma，安裝後即可使用，不需要額外配置。
- **整合式：** 使用整合好的 Dotfiles 套件，快速取得預先配置的桌面外觀與操作體驗。
- **半自訂：** 選擇適合的 Desktop Shell，搭配需要的桌面工具。Shell 通常整合狀態列、應用程式啟動器、通知、控制中心與桌面小工具等介面，再依需求調整外觀與功能。
- **全手工：** 自行安裝與設定桌面所需的工具與外觀，例如桌布、字型、應用程式圖示、游標、GTK 主題（調整應用程式的配色與元件外觀）、視窗裝飾、Waybar（狀態列）、Rofi（應用程式啟動器）、Wlogout（登出選單），以及 Picom（X11 畫面合成器）等，從功能到外觀逐步打造自己的桌面體驗。
