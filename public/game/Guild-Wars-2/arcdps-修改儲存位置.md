2026-10-17 Created

---

## 修改 arcdps 儲存路徑

1. 關閉遊戲

2. 打開 \Guild Wars 2\addons\arcdps\arcdps.ini

3. 找到 boss_encounter_path=，修改為：
```text
boss_encounter_path=F:\Guild Wars 2 Log\addons\arcdps\arcdps.cbtlogs
```
## 修改 ArcdpsLog Manager 讀取路徑

開啟 ArcdpsLog Manager > Settings > Settings > Remove 舊路徑 > Add a Log directory > 貼上新路徑 > Save

## 驗證結果

開啟遊戲進訓練場測試 (開啟魔像 > 打一下 > 關閉魔像離開戰鬥 > 檢查有沒有更新 log)

## 注意事項

arcdps 的儲存路徑與 ArcDPSLog Manager 的讀取路徑是兩項獨立設定。 搬移日誌資料夾時，兩邊都要修改，否則管理器可能無法顯示新紀錄。

