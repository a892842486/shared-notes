2026-10-17 Created

---

# Linux 發行版（Distributions）

## Debian 系

| 發行版 | 特色 | 基礎／關係 |
|---|---|---|
| [Debian](https://www.debian.org/) | 著重穩定性，是許多發行版的基礎。 | 獨立發行版 |
| [Ubuntu LTS](https://ubuntu.com/about/release-cycle) | 操作簡潔，以活動總覽和工作區管理視窗，預設使用 GNOME。 | 基於 Debian |
| [Linux Mint](https://linuxmint.com/) | 介面類似 Windows，採用傳統的開始選單、工作列與系統匣，預設使用 Cinnamon。 | 基於 Ubuntu（另有 LMDE 版本基於 Debian） |
| [Pop!_OS](https://system76.com/pop) | 著重生產力，支援自動排列視窗，預設使用 COSMIC。 | 基於 Ubuntu |

## Arch 系

| 發行版 | 特色 | 基礎／關係 |
|---|---|---|
| [Arch Linux](https://archlinux.org/) | 基礎安裝精簡，由使用者自行選擇桌面環境與系統組件。 | 獨立發行版 |
| [CachyOS](https://cachyos.org/) | 著重效能調校與遊戲體驗，提供多種桌面環境與視窗管理器選項。 | 基於 Arch |
| [EndeavourOS](https://endeavouros.com/) | 提供較容易上手的 Arch 安裝體驗，同時保留 Arch 的使用方式。 | 基於 Arch |
| [Manjaro](https://manjaro.org/) | 提供圖形化安裝與系統管理工具，降低入門門檻。 | 基於 Arch |

## Red Hat 生態系

| 發行版 | 特色 | 基礎／關係 |
|---|---|---|
| [Fedora](https://fedoraproject.org/) | 更新較積極，較早採用許多新技術。 | 獨立發行版，與 Red Hat 生態系密切相關 |

## 其他發行版

| 發行版 | 特色 | 基礎／關係 |
|---|---|---|
| [Alpine Linux](https://alpinelinux.org/) | 輕量、安全，常用於容器與伺服器環境。 | 獨立發行版 |
| [openSUSE](https://www.opensuse.org/) | 提供不同的發行版本與更新策略，並具備完整的系統管理工具。 | 獨立發行版 |
| [NixOS](https://nixos.org/) | 以宣告式配置與可重現的系統環境著稱。 | 獨立發行版 |

## 補充觀念

- **Linux Kernel（Linux 核心）：** 負責管理硬體、記憶體、程序等系統資源。
- **Linux Distribution（Linux 發行版）：** 將 Linux 核心、系統工具、套件管理與其他軟體整合成可使用的作業系統。
- **Desktop Environment（DE，桌面環境）：** 例如 GNOME、KDE Plasma、Cinnamon、COSMIC。
- **Window Manager（WM，視窗管理器）：** 負責管理視窗的排列、移動與顯示，例如 Hyprland、Niri。
- **LMDE（Linux Mint Debian Edition）：** Linux Mint 的 Debian 版本，直接基於 Debian，而非 Ubuntu，提供與一般 Linux Mint 相似的桌面使用體驗。

> Linux 嚴格來說是核心；Ubuntu、Fedora、Arch Linux 等才是完整的 Linux 發行版。