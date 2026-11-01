2026 10 21 Created

---

# CachyOS 雙系統安裝與基本設定

## 1. 製作雙系統：切出 Linux 系統區

1. 按 `Win + X`，選擇「磁碟管理」。
2. 對要安裝 Linux 的 SSD 按右鍵，選擇「壓縮磁碟區」。
3. 輸入壓縮大小 `40960 MB`（約 40 GiB），等待完成。
4. 保留產生的未配置空間，供 Linux 安裝使用。

## 2. 製作 USB 開機碟

### 下載工具與系統映像檔

- [Rufus 可攜版下載](https://rufus.ie/zh_TW/#download)
- [CachyOS Desktop Edition 下載](https://cachyos.org/download/)
  - 選擇 Desktop Edition。
  - Handheld Edition 是為掌機等裝置準備的版本。

### Rufus 設定

1. 開啟 Rufus，選擇 USB 隨身碟。
2. 選擇下載的 `cachyos-desktop-linux-260809.iso`。
3. 設定：
   - 磁碟分割配置：GPT
   - 目標系統：UEFI
   - 檔案系統：FAT32（依 Rufus 實際提供的選項）
4. 開始製作 ISO 開機碟。

USB 隨身碟：[PChome 購買連結](https://24h.pchome.com.tw/prod/DGCD15-A900AVWCF)

## 3. 設定開機順序

1. 插入 USB，重新啟動電腦。
2. 開機時：
   - `F12`：GIGABYTE 主機板的開機裝置選單。
   - `Delete`：進入 BIOS 設定。
3. 若進入 BIOS，前往「BIOS 功能」→「啟動優先順序」。
4. 將 USB（CachyOS）設為優先開機裝置。
5. 儲存設定並離開。

## 4. 安裝 CachyOS

1. 在開機選單選擇 `CachyOS`。
2. 進入 Live 桌面後，選擇「啟動安裝程式」。
3. 鍵盤設定：下一步。
4. 開機載入程式：選擇 `GRUB`。
5. 磁碟分割：選擇「手動分割」。

### 建立 EFI 開機分割區

在先前切出的未配置空間中建立：

- 容量：`512 MiB`
- 類型：基本分割區
- 檔案系統：`FAT32`
- 掛載點：`/boot/efi`
- 勾選：`boot`
- 按「OK」。

### 建立 Linux 系統分割區

- 容量：填入剩餘空間（約 `40000 MiB`，依實際可用容量調整）
- 類型：基本分割區
- 檔案系統：`Btrfs`
- 掛載點：`/`
- 其他選項：不勾選
- 按「OK」。

選擇系統分割區，按「下一步」。

### 選擇桌面環境

- **Plasma Desktop / GNOME**：完整桌面功能。
- **XFCE4**：資源需求較低。
- **Hyprland**：自訂、輕量的 Wayland 視窗管理環境。

本次選擇：**Hyprland**。

### 設定使用者

1. 設定使用者名稱。
2. 設定電腦名稱。
   - 使用者名稱：`Bamu`
   - 電腦名稱：`bamu-linux`
3. 管理員密碼使用相同密碼。
4. 按「下一步」，確認設定後開始安裝。
5. 安裝完成後重新啟動，拔除 USB。

完成後即可進入 CachyOS 桌面。

## 5. 桌面環境與基本操作

本次使用的是 CachyOS 預先配置的 **Hyprland + Noctalia Shell**。

- **Hyprland**：管理視窗、工作區、快捷鍵與視窗邊框。
- **Noctalia Shell**：提供狀態列、控制中心、桌布與桌面設定。

### 常用快捷鍵

| 操作 | 快捷鍵 |
|---|---|
| 開啟終端機 | `Super + Enter` |
| 關閉視窗 | `Super + Q` |
| 移動視窗 | `Super + 滑鼠左鍵拖曳` |
| 開啟 Noctalia 設定 | `Super + Z`（依目前配置） |
| 截圖 | 待確認目前配置 |

`Super` 是鍵盤上的 Windows 鍵。

### 雙螢幕設定

在 Noctalia 設定中前往「桌布」→「螢幕」，可以看到各螢幕的設定。

本次設定：

- `DVI-D-1`：`secondary`
- `HDMI-A-1`：`primary`

將需要作為主螢幕的顯示器設為 `primary`。

### 外觀設定

在 Noctalia 設定的「外觀」中調整主題與調色盤。

- `Theme Mode`：選擇 `Dark`。
- `Palette Source`：選擇 `Built-In` 或其他喜歡的調色盤來源。
- `Wallpaper`：管理桌布。
- `邊框`：調整 Noctalia 內部按鈕、輸入框、彈出視窗與卡片的邊框。

注意：Hyprland 視窗本身的焦點邊框顏色，可能需要另外調整 Hyprland 設定。

## 6. 安裝繁體中文注音

本次使用 **Fcitx5 + 新酷音（Chewing）**。

### 安裝套件

開啟終端機（`Ctrl + Alt + T`），執行：

```bash
sudo pacman -S fcitx5 fcitx5-chewing fcitx5-configtool
```

### 確認套件是否安裝

```bash
pacman -Q fcitx5 fcitx5-chewing fcitx5-configtool
```

如果列出套件名稱與版本，即代表對應套件已安裝。

### 開啟輸入法設定

```bash
fcitx5-configtool
```

在設定工具中加入 `Chewing`（新酷音），即可使用注音輸入繁體中文。

若輸入法無法在其他應用程式中使用，再檢查 Fcitx5 是否啟動，以及 Hyprland 的輸入法環境變數。

## 7. 開機時選擇 Windows 或 CachyOS

本次使用的開機管理器為 **GRUB 2.16**。

### 確認 GRUB 狀態

```bash
bootctl status
```

本次確認結果：

- Current Boot Loader：`GRUB 2.16`
- 已偵測到 `Windows Boot Manager`。

### 檢查 GRUB 開機選單設定

```bash
grep -E '^(GRUB_TIMEOUT|GRUB_TIMEOUT_STYLE|GRUB_DISABLE_OS_PROBER)=' /etc/default/grub
```

本次設定：

```bash
GRUB_TIMEOUT='5'
GRUB_TIMEOUT_STYLE=menu
GRUB_DISABLE_OS_PROBER='false'
```

### 重新產生 GRUB 設定

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

確認 Windows 開機項目：

```bash
sudo grep -n "Windows Boot Manager" /boot/grub/grub.cfg
```

本次已確認設定檔中存在 Windows Boot Manager 項目，但開機仍直接進入 CachyOS，因此尚未完成開機選單的顯示設定。

### 暫時進入 Windows

GIGABYTE 主機板可在開機時按 `F12` 開啟一次性開機裝置選單，再選擇 `Windows Boot Manager`。

若要改變預設開機系統，則可在 BIOS 的「啟動優先順序」中調整。

---

## 8. 待完成事項

- [ ] 設定 GRUB 開機選單，讓每次開機都能選 Windows 或 CachyOS。
- [ ] 確認預設使用 `Hyprland (uwsm-managed)`，避免每次手動選擇工作階段。
- [ ] 評估是否啟用自動登入。
- [ ] 設定截圖快捷鍵。
- [ ] 確認 Fcitx5 注音輸入法的切換快捷鍵。
