2026 10 21 Created

---

# Linux 遊戲工具入門

## 工具比較

steam > 自動安裝 Proton 相容 windows 遊戲
獨立安裝遊戲 > Wine (相容層)。再選擇管理工具(如下)

- Lutris 遊戲管理器
- Bottles 獨立環境管理器 (不止管遊戲，還能執行其他 Windows程式)
- PortProton 遊戲執行工具
- Faugus Launcher 遊戲管理器


## 安裝原則

1. 先安裝 CachyOS，確認 NVIDIA 顯示卡驅動正常。
2. 透過 CachyOS Hello 的 `Install Gaming packages` 安裝基本遊戲套件與常用工具。
3. Steam 遊戲先用內建 Proton。
4. 官網版遊戲再選擇 Lutris 或 Bottles 測試。
5. 先確認遊戲能正常登入、更新與執行，再考慮最佳化。

## 注意事項

- Proton、PortProton、Lutris、Bottles 是不同工具，不必全部安裝。
- 遊戲能啟動不代表所有功能都正常；更新、登入器、反作弊與第三方模組可能有相容性問題。
- CachyOS 也提供自己的 Proton 與 Wine 版本，但新手可先使用預設方案，遇到問題再調整。

## 官方資源

- [CachyOS 遊戲指南](https://wiki.cachyos.org/configuration/gaming/)
- [Proton（GitHub）](https://github.com/ValveSoftware/Proton)
- [Lutris](https://lutris.net/)
- [Bottles](https://usebottles.com/)
- [PortProton（GitHub）](https://github.com/Castro-Fidel/PortWINE)
