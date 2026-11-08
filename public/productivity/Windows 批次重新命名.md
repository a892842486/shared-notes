2026 11 06 Created

---

# Windows 批次重新命名（加入前綴）

將多個檔案名稱前面加入指定文字，例如 `文書 `，保留原始檔名與副檔名。

## 方法一：PowerShell

在目標資料夾右鍵 →「在終端機中開啟」。

**所有檔案加入前綴：**

```powershell
Get-ChildItem -File | Rename-Item -NewName { "文書 " + $_.Name }
```

**避免重複加入前綴（推薦）：**

```powershell
Get-ChildItem -File |
Where-Object { $_.Name -notlike "文書 *" } |
Rename-Item -NewName { "文書 " + $_.Name }
```

僅修改目前資料夾內的檔案，不包含子資料夾。

## 方法二：PowerToys PowerRename（圖形介面）

1. 選取多個檔案 → 右鍵 → PowerRename。
2. 勾選「使用規則運算式」。
3. 搜尋：`^` (^ 代表開頭、$ 代表結尾)
4. 取代為：`文書 `（包含尾端空白）。
5. 套用至：「只有檔案名稱」。
6. 確認預覽 → 套用。

`^` 代表檔案名稱開頭。

## 修改範例

| 修改前 | 修改後 |
|---|---|
| 報告.pdf | 文書 報告.pdf |
| 筆記.txt | 文書 筆記.txt |

**建議：** 偶爾使用選 PowerShell；經常批次整理選 PowerRename。