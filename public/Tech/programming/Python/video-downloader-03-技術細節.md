2026-11-12 Created

---

# Video Downloader 檔案詳解

## manifest.json｜Chrome Extension 設定入口

**用途：** 定義擴充功能的基本資訊、權限與程式載入方式。

### 核心架構

1. **基本設定**

    - `manifest_version: 3`：使用 Chrome Manifest V3。
    - `action`：點擊圖示時，開啟 `popup/popup.html`。

2. **權限管理**

    - `permissions`：允許使用分頁、儲存、網路監聽及 Native Messaging 等 Chrome API。
    - `host_permissions`：允許存取 HTTP、HTTPS 網站。

3. **程式載入**

    - `content_scripts`：將 `content.js`、`content.css` 注入網頁。
    - `background.service_worker`：指定 `background.js` 處理背景事件。

### 資料流程

`Chrome 載入擴充功能` → `讀取 manifest.json` → `載入背景程式、網頁腳本與 Popup 設定`

### 互動關係

- `background.js`：指定為背景 Service Worker。
- `content.js / content.css`：指定注入網頁的程式與樣式。
- `popup.html`：指定點擊擴充功能圖示後開啟的視窗。

## background.js｜背景事件與下載任務管理

**用途：** 負責偵測影片串流來源、管理下載任務，以及 Chrome 與 Python 之間的通訊。

### 核心架構

1. **影片偵測**

    - `chrome.webRequest`：監聽網路請求，偵測 HLS（`.m3u8`）影片來源。
    - `hlsSources`：依分頁記錄影片網址，切換或關閉分頁時清除。

2. **下載任務管理**

    - `tasks`：管理下載任務、進度與狀態。
    - `chrome.storage.local`：保存下載紀錄，背景程式重啟後可恢復。
    - `saveQueue`：依序儲存，避免非同步寫入衝突。

3. **Native Messaging**

    - `connectDownload()`：每個任務建立獨立連線，將下載指令傳給 Python。
    - `port.onMessage`：接收下載進度、完成或錯誤等事件。
    - `port.onDisconnect`：處理連線中斷，更新任務狀態。

4. **下載控制**

    - `startDownload()`：檢查網址與重複任務，準備 HLS 備援來源，建立並啟動下載。
    - `cancelDownload()`：更新取消狀態、中斷連線並保存紀錄。

5. **Chrome 訊息入口**

    - `chrome.runtime.onMessage`：接收其他擴充功能程式的指令。
    - 支援 `download`、`getDownloadTasks`、`cancelDownload`。

### 資料流程

**下載指令：**

`content.js` → `background.js` → `Native Messaging` → `host.py` → `downloader.py`

**下載進度：**

`downloader.py` → `host.py` → `background.js` → `Chrome Storage` → `popup.js`

### 互動關係

- `manifest.json`：指定 `background.js` 為背景 Service Worker。
- `content.js`：接收網頁下載按鈕發送的下載請求。
- `popup.js`：提供下載任務資料，接收取消下載指令。
- `chrome.storage.local`：保存下載任務與狀態，供 Popup 讀取。
- `host.py`：`background.js` 透過 Native Messaging 向它傳送下載指令，並接收回傳的進度與結果。

## content.js｜網頁影片偵測與下載按鈕

**用途：** 偵測網頁中的影片播放器、顯示下載按鈕，並將下載請求交給 `background.js`。

### 核心架構

1. **影片偵測**

    - `detectVideos()`：尋找網頁中的 `<video>` 元素。
    - `MutationObserver`：監聽網頁內容變化，偵測動態載入的影片。

2. **下載按鈕管理**

    - `addDownloadButton()`：為影片建立下載按鈕，避免重複建立。
    - `updateButtonPosition()`：依照影片位置調整按鈕，影片不在畫面內時隱藏。
    - `updateAllButtons()`：更新按鈕位置，並清除已移除影片的按鈕。

3. **下載請求**

    - `chrome.runtime.sendMessage()`：將網頁網址、標題與下載路徑傳給 `background.js`。
    - `background.js`：統一管理下載任務。

4. **設定同步**

    - `chrome.storage.local`：讀取下載路徑與按鈕顯示設定。
    - `chrome.storage.onChanged`：同步 Popup 修改的按鈕開關。

### 資料流程

`網頁 <video>` → `content.js 偵測` → `顯示下載按鈕` → `使用者點擊` → `background.js`

### 互動關係

- `manifest.json`：指定將 `content.js` 注入網頁。
- `content.css`：負責下載按鈕的外觀。
- `popup.js`：透過 Chrome Storage 共用下載路徑與按鈕開關設定。
- `background.js`：接收下載請求，負責後續任務管理與 Python 通訊。

## content.css｜網頁下載按鈕樣式

**用途：** 設定網頁中「⬇ 下載」按鈕的外觀。

- 設定按鈕顏色、大小、陰影與滑鼠懸停效果。
- 使用 `position: fixed`，讓按鈕能依照影片位置顯示。
- 使用高 `z-index`，避免被網頁其他元素遮擋。

**互動關係：**

- `content.js`：負責建立按鈕、控制位置與顯示狀態。
- `content.css`：負責按鈕的視覺樣式。

## popup.html｜擴充功能視窗結構

**用途：** 定義點擊 Chrome 擴充功能圖示後顯示的操作介面。

- **下載按鈕開關**：控制網頁中是否顯示下載按鈕。
- **下載位置**：讓使用者設定影片儲存路徑。
- **下載清單**：顯示下載任務、數量與狀態。
- **狀態訊息**：顯示操作提示。

**互動關係：**

- `manifest.json`：指定開啟 `popup.html`。
- `popup.css`：負責視窗外觀。
- `popup.js`：負責介面操作、設定與下載任務顯示。

## popup.css｜擴充功能視窗樣式

**用途：** 設定 Popup 視窗的外觀與排版，採用深色主題。

- **基本樣式**：視窗大小、背景、文字、輸入框與按鈕。
- **設定開關**：下載按鈕的切換動畫與顏色。
- **下載清單**：任務卡片、進度條、下載狀態與取消按鈕。
- **狀態顏色**：區分下載完成、失敗、跳過與取消等狀態。

**互動關係：**

- `popup.html`：提供介面結構。
- `popup.js`：控制介面內容與狀態。
- `popup.css`：負責視覺樣式。

## popup.js｜擴充功能視窗互動

**用途：** 管理 Popup 操作介面、使用者設定與下載任務顯示。

### 核心架構

1. **使用者設定**

    - `loadSettings()`：讀取下載路徑與下載按鈕開關。
    - `chrome.storage.local`：儲存設定，並同步給 `content.js`。

2. **下載任務顯示**

    - `renderTask()`：建立任務卡片，顯示進度、速度、剩餘時間與狀態。
    - `renderTasks()`：更新下載清單，依建立時間排序。
    - `chrome.storage.onChanged`：監聽任務變化，即時更新畫面。

3. **下載控制**

    - `chrome.runtime.sendMessage()`：向 `background.js` 查詢任務或發送取消下載指令。
    - `loadDownloadTasks()`：取得已保存的下載紀錄。

### 資料流程

- `Popup` → `Chrome Storage` → 保存設定。
- `Popup` ↔ `background.js` → 查詢任務、取消下載。
- `background.js` → `Chrome Storage` → `Popup` 更新下載狀態。

### 互動關係

- `popup.html`：提供視窗結構。
- `popup.css`：負責視窗外觀。
- `content.js`：透過 Chrome Storage 共用下載按鈕設定。
- `background.js`：提供下載任務資料與控制功能。

## host.example.json / host.json｜Native Host 設定

**用途：** 告訴 Chrome 如何找到本機 Python 程式，以及允許哪些擴充功能連線。

- `host.example.json`：提供其他使用者參考的設定範本。
- `host.json`：實際使用的設定檔，包含本機啟動路徑與 Extension ID。
- `name`：Native Host 名稱，對應 `background.js` 的 `HOST_NAME`。
- `path`：指定啟動程式 `host.bat`。
- `allowed_origins`：限制哪些 Chrome Extension 可以連線。
- `type: stdio`：使用標準輸入與輸出傳遞訊息。

**互動關係：**

`background.js` → `Chrome Native Messaging` → `host.json`（Chrome 查找註冊設定）→ `host.bat` → `host.py`

## host.bat｜Python 啟動腳本

**用途：** 讓 Chrome 在 Windows 上啟動 Python Native Host。

- `py -3.13`：使用 Python 3.13 執行程式。
- `%~dp0`：取得批次檔所在資料夾路徑，確保能找到 `host.py`。

**互動關係：**

`host.json` → `host.bat` → `host.py`

## host.py｜Native Messaging 通訊入口

**用途：** 接收 Chrome 傳來的指令，呼叫 Python 下載模組，再回傳下載結果。

### 核心架構

1. **訊息接收與回傳**

    - `read_message()`：讀取 Chrome 傳來的 JSON 訊息。
    - `send_message()`：將 Python 的執行結果傳回 Chrome。
    - 使用 `stdin / stdout` 與 Chrome 雙向通訊。

2. **指令處理**

    - `main()`：持續接收與處理 Chrome 指令。
    - `ping`：測試 Native Host 是否正常連線。
    - `download`：呼叫 `downloader.py` 執行影片下載。

3. **下載結果回報**

    - `on_progress()`：將下載進度回傳 Chrome。
    - 回傳 `completed`、`skipped`、`error` 等事件。
    - `requestId`：讓 Chrome 知道訊息屬於哪個下載任務。

### 資料流程

`background.js` → `Native Messaging` → `host.py` → `downloader.py`

下載進度與結果沿相反方向回傳。

### 重要觀念

- `Native Host`：在電腦上執行、負責與 Chrome 擴充功能通訊的程式。
- `stdin / stdout`：程式接收與輸出訊息的管道。
- `JSON`：Chrome 與 Python 交換資料的格式。
- `struct`：處理 Native Messaging 要求的訊息長度資訊。
- `progress_callback`：下載過程中，讓 `downloader.py` 回報進度。

## downloader.py｜影片下載核心

**用途：** 使用 yt-dlp 解析與下載影片，處理畫質、檔案命名、下載進度及備援來源。

### 核心架構

1. **下載準備**

    - `download_video()`：接收影片網址、下載路徑與相關參數。
    - 驗證網址與路徑，自動建立下載資料夾。

2. **yt-dlp 下載設定**

    - `yt_dlp.YoutubeDL()`：使用 yt-dlp 的 Python API 下載影片。
    - 優先選擇最佳畫質與音訊，必要時透過 FFmpeg 合併為 MP4。
    - 自訂檔名格式：`影片標題 [解析度].副檔名`。
    - 支援續傳，避免覆寫既有檔案。

3. **下載進度與結果**

    - `report_progress()`：每秒回報下載進度、速度、檔案大小與剩餘時間。
    - `progress_callback`：將下載資訊交給 `host.py`，再回傳 Chrome。
    - 下載完成回傳結果，失敗則回傳錯誤資訊。

4. **下載備援與標題處理**

    - 優先解析原始網址，下載失敗時嘗試 HLS 備援網址。
    - `Referer`：提供來源網站資訊，協助處理部分網站的存取限制。
    - `fix_video_title()`：當 yt-dlp 無法取得合適標題時，使用網頁標題替代。

### 資料流程

`host.py` → `download_video()` → `yt-dlp` → `FFmpeg（需要時）` → `本機影片檔案`

下載進度與結果：`downloader.py` → `host.py` → `background.js` → `popup.js`

### 重要觀念

- `yt-dlp`：負責解析影片來源與執行下載的核心工具。
- `FFmpeg`：負責影音合併與必要的格式處理。
- `progress_hooks`：yt-dlp 提供的下載進度回報機制。
- `fallback_url`：主要網址下載失敗時，改用備援 HLS 來源。
- `PostProcessor`：yt-dlp 的處理機制，這裡用於修正影片標題。
- `stdout / stderr`：將一般輸出導向 stderr，避免干擾 Native Messaging 通訊。
