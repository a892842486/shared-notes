2026-11-12 Created

---

# Video Downloader 運作流程

## 1. 架構

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
    ├── downloader.py      # 使用 yt-dlp 下載、合併與儲存影片
    ├── host.bat           # 啟動 Python 程式
    ├── host.json          # 告訴 Chrome 程式在哪裡、誰能連線
    └── host.example.json  # 提供其他使用者的設定範本
```

## 2. 開啟 Chrome 擴充功能

使用者點擊 Chrome 工具列上的 Video Downloader 圖示。

- `manifest.json`：告訴 Chrome 要開啟哪個視窗、載入哪些程式。
- `popup.html`：建立視窗內容，例如設定、下載清單。
- `popup.css`：決定視窗的深色主題、顏色與排版。
- `popup.js`：讓設定開關、下載清單與取消按鈕能夠運作。

## 3. 瀏覽網頁、偵測影片

使用者打開有影片的網頁。

- `content.js`：尋找網頁播放器，建立下載按鈕。
- `content.css`：決定網頁下載按鈕的外觀。
- `background.js`：在背景監聽影片串流來源，記錄可用的 HLS 網址。

## 4. 點擊下載按鈕

使用者點擊下載，Chrome 開始建立下載任務。

- `content.js`：將下載請求傳給 `background.js`。
- `background.js`：建立任務、檢查重複下載、準備備援網址。
- `background.js`：透過 Native Messaging 將下載指令傳給 Python。

## 5. Python 實際下載影片

Chrome 將下載工作交給電腦端的 Python。

- `host.json`：記錄 Native Host 的名稱、啟動路徑與允許連線的擴充功能。
- `host.bat`：啟動 `host.py`。
- `host.py`：接收 Chrome 的下載指令，交給 `downloader.py`。
- `downloader.py`：使用 yt-dlp 下載影片，必要時透過 FFmpeg 合併影音。

`host.example.json`：提供其他使用者安裝時參考的設定範本。

## 6. 顯示下載進度與結果

影片開始下載，使用者可以在 Popup 查看進度。

- `downloader.py`：產生下載進度、速度與完成結果。
- `host.py`：將這些資訊傳回 Chrome。
- `background.js`：更新下載任務，並儲存紀錄。
- `popup.js`：讀取任務狀態，更新下載清單與進度條。

---

## 完整下載流程

```text
使用者瀏覽網頁
    ↓
content.js          偵測影片、顯示下載按鈕
    ↓
background.js       建立並管理下載任務
    ↓
Native Messaging    Chrome 與 Python 通訊
    ↓
host.py             接收下載指令
    ↓
downloader.py       呼叫 yt-dlp / FFmpeg
    ↓
本機資料夾           儲存影片
```

下載進度則沿著相反方向回傳，最後顯示在 Popup 視窗中。

## 核心分工

- **Chrome Extension**：負責網頁偵測、使用者介面、下載任務管理。
- **Python**：負責接收指令、下載影片、回報結果。
- **yt-dlp**：負責解析影片來源與下載。
- **FFmpeg**：負責影音合併與處理。