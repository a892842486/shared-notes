2026 11 08 Created

---

# Video Downloader 影片下載器 

## 簡介

Video Downloader 是一款 Chrome 擴充功能，能偵測網頁影片，讓使用者直接下載並管理下載進度。

透過 Python 整合 yt-dlp 與 FFmpeg，負責影片下載、影音合併與檔案儲存。

yt-dlp 用來「找影片、下載影片」，FFmpeg 用來「處理影片、合併影音」。

## 架構

```text
video_downloader/
│
├── manifest.json          # 擴充功能入口：設定權限、載入哪些檔案
├── background.js          # 下載管理中心：接收指令、管理任務與下載狀態
│
├── content/                # 網頁上的下載按鈕
│   ├── content.js         # 偵測網頁影片、建立下載按鈕
│   └── content.css        # 下載按鈕的外觀
│
├── popup/                  # 點擊 Chrome 擴充功能圖示後的視窗
│   ├── popup.html         # 視窗有哪些功能與區塊
│   ├── popup.css          # 視窗的深色主題、按鈕與排版
│   └── popup.js           # 操作設定、顯示下載進度與取消下載
│
└── native/                 # 電腦端：負責真正下載影片
    ├── host.py            # Chrome 與 Python 的溝通橋樑
    ├── downloader.py      # 使用 yt-dlp 下載影片，必要時透過 FFmpeg 合併影音
    ├── host.bat           # 啟動 Python 程式
    ├── host.json          # 告訴 Chrome 程式在哪裡、誰能連線
    └── host.example.json  # 提供其他使用者的設定範本
```

## 詳細介紹

### 主要功能
- 偵測網頁影片，提供下載按鈕。
- 自動選擇最佳畫質，支援影音合併。
- 顯示下載進度、速度與剩餘時間。
- 支援多任務下載、取消下載，並可嘗試續傳中斷的任務。
- 自訂下載位置，保存下載紀錄。
- 可隱藏下載按鈕。

### 使用情境
- 瀏覽網頁時，直接點擊下載按鈕儲存影片。
- 透過 Chrome 擴充功能視窗，查看與管理下載任務。
- 下載完成後，將影片儲存至指定的電腦資料夾。

### Chrome Extension 與 Python 分工

> Chrome 負責讓使用者操作與管理下載，Python 負責真正把影片下載到電腦。

#### Chrome Extension｜操作與管理
- 偵測網頁影片、提供操作介面。
- 管理下載任務、顯示進度與狀態。
- 透過 Native Messaging 向 Python 傳送下載指令。

#### Python｜實際下載與處理
- 使用 yt-dlp 解析影片來源、選擇畫質並下載。
- 使用 FFmpeg 合併影音。
- 處理檔案命名、儲存與下載進度回報。
