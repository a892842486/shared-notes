2026 10 17 Created

---

# Linux 桌面環境與視窗管理器

## 桌面環境比較

| 名稱 | 特色 | 適合對象 |
|---|---|---|
| [GNOME（Fedora Workstation）](https://fedoraproject.org/workstation/) | 簡潔、操作一致，預設介面較偏向活動總覽與工作區切換，並整合觸控手勢、虛擬鍵盤與滑動切換工作區等操作。 | 喜歡簡潔、整合度高，或需要觸控螢幕操作的使用者 |
| [Cinnamon](https://github.com/linuxmint/cinnamon) | 傳統 Windows 風格，簡單好用。 | 一般電腦、想直接使用 |
| [KDE Plasma](https://kde.org/zh-tw/plasma-desktop/) | 傳統桌面操作、高度客製化、外觀調整選項豐富。 | 一般電腦、喜歡美化桌面 |
| [Xfce](https://www.xfce.org/) | 傳統桌面風格、輕量、實用。 | 舊電腦、偏好簡潔配置 |
| [LXQt](https://lxqt-project.org/) | 輕量、傳統桌面風格，著重節省資源。 | 資源有限的舊電腦 |
| [COSMIC（Pop!_OS）](https://system76.com/cosmic/download) | 現代化介面、工作區管理、視窗平鋪與多工操作。 | 喜歡現代化桌面與多工操作 |

### 整合程度：高

通常已整合多種桌面功能：

- 視窗管理與桌面工作區
- 面板、工具列或系統托盤
- 系統設定工具
- 應用程式選單或啟動介面
- 通知、桌面背景及其他桌面功能

安裝後通常就能直接使用，不必自行逐一組裝所有桌面元件。

## 視窗管理器／Wayland 合成器比較

| 名稱 | 特色 | 適合對象 |
|---|---|---|
| [Hyprland](https://github.com/hyprwm/hyprland) | 視窗平鋪、動畫效果、鍵盤操作與高度客製化。 | 喜歡自己組裝桌面、追求個人化 |
| [Sway](https://github.com/swaywm/sway) | 極簡、平鋪式視窗管理、鍵盤操作。 | 喜歡簡潔、偏向 i3 操作方式 |
| [Niri](https://github.com/niri-wm/niri) | 採用水平捲動式視窗排列，適合管理大量視窗。 | 喜歡不同於傳統平鋪的工作流程 |

### 整合程度：較低

這些 Wayland 合成器主要負責視窗管理、畫面合成與顯示輸出，通常不會像完整桌面環境一樣，整合所有桌面工具。

可以選擇自行安裝與設定各種工具，也可以使用別人整合好的 **Dotfiles**，快速套用預先配置的桌面外觀與操作方式。

**常見的 Dotfiles 專案：**

| 名稱 | 特色 |
|---|---|
| [end-4 / dots-hyprland](https://github.com/end-4/dots-hyprland) | 動態配色、流暢動畫與豐富的桌面功能。 |
| [ML4W Dotfiles](https://github.com/mylinuxforwork/dotfiles) | 提供多種主題與桌面設定，可調整面板、模糊效果、動畫與快捷鍵。 |
| [Caelestia Dot](https://github.com/caelestia-dots/caelestia) | 動態配色、流暢動畫與現代化桌面介面。 |
| [bitzdots](https://github.com/bitzzdev/bitzdots) | 著重輕量化與整體視覺一致性，可隨桌布同步多種元件的配色。 |
| [hyprland-glassmorphism](https://github.com/v-o-h-s/hyprland-glassmorphism) | 半透明磨砂玻璃、模糊背景與簡約的黑白灰配色。 |

**自行組裝時，可能需要另外選擇與設定：**

1. **狀態列：** [Waybar](https://github.com/alexays/waybar)
2. **應用程式啟動器：** [Rofi](https://github.com/davatorium/rofi)
3. **登出選單：** [Wlogout](https://github.com/ArtsyMacaw/wlogout)
4. **通知系統：** [Mako](https://github.com/eugeniosegala/MAKO)、[SwayNotificationCenter](https://github.com/ErikReider/SwayNotificationCenter)
5. **鎖定畫面：** [Hyprlock](https://github.com/hyprwm/hyprlock)、[Swaylock](https://github.com/swaywm/swaylock)
6. **桌布工具：** 依使用的合成器選擇
7. **螢幕截圖工具：** [Grim](https://github.com/emersion/grim)、[Slurp](https://github.com/emersion/slurp)

實際需要配置哪些工具，取決於使用的合成器、發行版，以及選擇的 Dotfiles 套件。

## 補充觀念

- **桌面環境（Desktop Environment, DE）：** 整合多種桌面功能，安裝後通常就能直接使用。
- **視窗管理器（Window Manager, WM）：** 負責視窗的排列、移動與管理，例如 i3、Openbox。
- **Wayland 合成器（Wayland Compositor）：** 負責管理 Wayland 視窗、畫面合成與顯示輸出；Hyprland、Sway、Niri 都屬於這一類，也提供視窗管理功能。
- **Rice／Dotfiles：** 透過設定檔調整桌面外觀、快捷鍵、工具與操作流程。
- **整合程度不等於客製化能力：** KDE Plasma 的整合程度高，但仍有很高的客製化自由；Hyprland 則讓使用者更直接地決定桌面元件如何組合。
