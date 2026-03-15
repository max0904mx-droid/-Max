# PawSense AI — 功能規格文檔

## 專案概覽

| 項目 | 內容 |
|------|------|
| 專案名稱 | PawSense AI（原 Yu Chen AI — Developer & Builder） |
| 作者 | Yu Chen AI (max0904mx-droid) |
| 技術棧 | HTML / CSS / JavaScript（純前端，無框架） |
| 外部資源 | Google Fonts（Noto Serif TC、Dancing Script、Space Mono、Syne、Instrument Serif、DM Sans） |
| 資料存儲 | localStorage |
| 部署方式 | GitHub Pages (`max0904mx-droid.github.io/-Max`) |
| 檔案結構 | 單一 `index.html`（約 1274 行） |
| 配色主題 | 淺藍色系（`#d0e8ff` 背景，藍色調 UI） |

**重要變更**：Portfolio 作品集頁面已移除，網站現為 PawSense AI 單頁應用，開啟即直接進入聊天介面。

---

## PawSense AI 聊天應用 (`#paws-app`)

### 1. 啟動機制

- 頁面載入後 `boot()` 自動執行：初始化用戶 → `initApp()` → `initVCat()` → 直接啟用 PawSense
- `#paws-app` 自動加上 `.active` class，虛擬貓咪自動顯示，body 滾動鎖定
- `showPawSense()` 和 `showPortfolio()` 為空函式（保留但無實作）
- 應用容器：`.app`，最大寬度 430px，全螢幕高度（手機 app 風格）

### 2. 側邊欄 (`#sidebar`)

- 寬度 272px，左側滑出（`transform:translateX`），有背景遮罩（`.sb-overlay`）
- **Logo**：PawSense 品牌圖示
- **新對話按鈕**（`.sb-new`）：`newChat()` 清除當前對話狀態
- **對話紀錄列表**（`.sb-list`）：`renderSB()` 動態生成，按時間倒序排列
  - 每項顯示模式圖示 + 標題 + 刪除按鈕
  - 點擊載入該對話：`loadSess(id)`
  - 刪除：`delSess(id)`
- **底部使用者資訊**：頭像 + 名稱 + 「清除資料」按鈕
- **清除資料**（`clearData()`）：confirm 確認後，清除 localStorage 中 `pawsense_v5`，重設為預設本地用戶
- **相關函式**：`toggleSB()`、`closeSB()`、`renderSB()`、`delSess()`

### 3. 聊天頂部欄 (`chat-hd`)

- 左：漢堡選單按鈕 → 開關側邊欄
- 中：標題（`#hd-title`，預設 "PawSense AI"）
- 右：語言切換按鈕（`#lang-btn`）+ 模式選擇按鈕（`.mode-chip`）
- 注：回首頁按鈕已移除

### 4. 聊天介面

#### 歡迎頁（`buildWelcome()`）
- 顯示於無訊息時
- Logo + 標題 + 副標題（「你的 AI 貓咪健康助理」）
- **功能介紹卡片**（`.feat-wrap`，新增）：4 張橫向卡片介紹核心功能
  - 🔍 行為分析 — 描述貓咪行為，AI 即時分析壓力指數與風險等級
  - 💊 健康問題 — 輸入症狀，獲得建議與就醫時機評估
  - 📋 分析紀錄 — 所有分析結果自動儲存，隨時查閱歷史報告
  - 🐈 貓咪管理 — 建立多隻貓咪檔案，追蹤每隻貓的健康狀況
- **分隔線**（`.feat-divider`）：「立即開始」文字 + 左右橫線
- **快速操作卡片**（`.sg-grid` 2x2）：
  - 🔍 行為分析 → `我想分析貓咪的行為`
  - 🐈 新增貓咪 → `幫我新增一隻貓咪`
  - 💊 健康問題 → `我的貓咪最近食慾不振`
  - 📊 查看紀錄 → `幫我看最近的分析結果`

#### 訊息氣泡
- **用戶訊息**（`.bb.user`）：右對齊，淺藍背景，圓角（右下方為直角）
- **AI 訊息**（`.bb.ai`）：左對齊，帶頭像（🐾），支援 markdown 格式化
- `fmtAI(text)`：將 `**粗體**` → `<strong>`、`# 標題` → `<h3>`、`- 列表` → `<li>`
- `esc(t)`：HTML 跳脫防 XSS
- 快速回覆按鈕（`.qr-row`）：AI 訊息底部的可點擊按鈕

#### 打字指示器
- 三點跳動動畫（`.ty-dots`），發送訊息後顯示，AI 回覆後移除

### 5. 四種聊天模式

透過底部彈出面板（`.sheet`）切換，`setMode(m)` / `setModeSilent(m)`：

| 模式 | ID | 圖示 | 功能 | 輸入提示 |
|------|----|------|------|---------|
| 貓咪問答 | `general` | 🐱 | 一般貓咪知識問答 | 有什麼想問的？ |
| 行為分析 | `analyze` | 🔍 | 搜尋獸醫文獻分析行為 | 描述貓咪的行為... |
| 報告解讀 | `report` | 📋 | 解讀過往分析報告 | 問我關於分析報告... |
| 貓咪管理 | `cat` | 🐈 | 新增/查看貓咪資料 | 說「新增貓咪」或「查看貓咪」... |

### 6. 靜態 AI 引擎 — `staticAI(txt, sys, mode)`

**核心機制**：關鍵字匹配，無 API 呼叫

**KB 知識庫物件**（`var KB = {...}`），包含 15+ 主題：

| 主題 key | 關鍵字 | 內容摘要 |
|----------|--------|---------|
| `morning_cry` | 早上、叫、哭、吵、清晨 | 清晨叫的原因（肚子餓、想陪伴、發情、老年認知） |
| `hide` | 躲、躲起來、藏、不出來 | 躲藏行為分析（壓力60、中風險） |
| `scratch` | 抓、咬、攻擊、兇 | 抓咬行為分析（壓力55、低至中風險） |
| `not_eat` | 不吃、不喝、食慾、沒胃口 | 食慾不振分析（壓力70、中至高風險） |
| `vomit` | 吐、嘔吐、反胃 | 嘔吐分析（壓力50、低至中風險） |
| `diarrhea` | 拉肚子、軟便、血便、血尿 | 排泄異常分析（壓力75、中至高風險） |
| `night_run` | 半夜、跑、狂奔、夜間 | Zoomies 分析（壓力15、低風險） |
| `box` | 紙箱、箱子、袋子、鑽 | 喜歡紙箱的原因 |
| `knead` | 踩奶、揉、按摩 | 踩奶行為（壓力5、正向） |
| `purr` | 呼嚕、打呼、咕嚕 | 呼嚕聲含義 |
| `tail` | 尾巴、搖尾巴 | 尾巴語言解讀 |
| `eye` | 眼神、瞇眼、慢眨眼 | 眼睛語言解讀 |
| `fee` | 費用、多少錢、獸醫費 | 醫療費用參考 |
| `food` | 吃什麼、飼料、罐頭、禁忌 | 飲食指南 |
| `sleep` | 睡覺、睡太多、嗜睡 | 睡眠分析 |
| `litter` | 砂盆、貓砂、廁所 | 砂盆問題分析 |
| `weight` | 體重、太胖、減重 | 體重管理 |
| `vaccine` | 疫苗、打針、驅蟲 | 疫苗與預防指南 |

**匹配邏輯**：
1. 遍歷 KB 各主題的 `keys` 陣列
2. 計算輸入文字包含多少個關鍵字（`score`）
3. 選擇分數最高的主題回傳其 `ans`
4. 若無匹配，嘗試正則比對（年齡/品種/新貓/多貓）
5. 最後 fallback：從 3 個通用回答中隨機選一個

**額外正則匹配**：
- `幾歲|壽命` → 貓咪壽命與年齡換算
- `品種|推薦|養什麼` → 品種推薦
- `新貓|第一次|剛養` → 新貓到家指南
- `多貓|兩隻|第二隻` → 多貓相處指南

### 7. 貓咪管理流程

#### `catFlow(txt, u)` — 管理模式路由
- `查看` / `我的貓` / `列出` → 顯示所有貓咪清單
- `新增` / `加入` → 進入 `catAddFlow()`
- `紀錄` / `報告` → 顯示最近 3 筆分析紀錄
- `切換到行為分析` → `setMode('analyze')`
- `解讀最新報告` → `setMode('report')`

#### `catAddFlow(txt, u)` — 逐步新增貓咪
5 步驟流程：

| 步驟 | `catAddStep` | 問題 | 回應處理 |
|------|-------------|------|---------|
| 1 | `name` | 貓咪的名字是？ | 存 `catTemp.name` |
| 2 | `breed` | 品種是什麼？ | 存 `catTemp.breed` |
| 3 | `age` | 年齡是？ | 存 `catTemp.age` |
| 4 | `gender` | 性別？ | 顯示快速回覆：公/母（已/未結紮） |
| 5 | `confirm` | 確認儲存？ | 確認→存入 `u.cats`；否則取消 |

新增後的貓咪物件結構：
```js
{ id: timestamp, name, breed, age, gender, emoji: '🐱', createdAt }
```

### 8. 行為分析結果卡片 (`buildResCard`)

當 analyze 模式下 AI 回覆包含「壓力指數 XX/100」和「風險等級 低/中/高」時，自動解析並生成結果卡片：

- **卡片頭部**：貓咪名稱 + 日期 + 風險等級 badge（顏色隨等級變化）
- **壓力指數進度條**：動畫從 0% 填充至實際值，顏色：≥60 紅色、≥35 黃色、<35 綠色
- **報告全文**：可滾動文字區
- **星級評分**（`.stars`）：5 顆星，`rateRes(id, score)` 儲存評分

分析結果同時存入 `u.records` 陣列：
```js
{ id, catId, catName, catEmoji, date, inputText, fullReport, sl(壓力值), risk, rating }
```

### 9. 照片上傳

- **觸發**：`triggerPhoto()` → 點擊隱藏的 `#photo-input`
- **處理**（`onPhotoSelect`）：FileReader 讀取為 base64 dataURL，最多 4 張
- **預覽列**（`#photo-bar`）：縮圖 60×60，可刪除
- **發送**：照片作為 `{type:'image', source:{type:'base64',...}}` 格式附加到訊息
- **聊天中顯示**：`<img class="msg-img">` 顯示在用戶氣泡中
- **變數**：`pendingPhotos[]`，含 `{dataUrl, base64, mediaType, name}`

### 10. 虛擬貓咪伴侶 (`#vcat-wrap`)

- **位置**：固定在右上角（`position:fixed; right:14px; top:70px`）
- **外觀**：圓形照片（`#vcat-photo`），預設為內嵌 base64 貓咪圖片
- **6 種心情**（`MOODS` 物件）：

| 心情 | 邊框色 | 動畫 | 對話氣泡範例 |
|------|--------|------|-------------|
| `idle` 好奇 | 金色 | 無 | 喵～有問題嗎？ |
| `happy` 開心 | 綠色 | 輕微彈跳 | 太好了！ |
| `excited` 興奮 | 綠色 | 快速彈跳 | 哇！太有趣了！ |
| `thinking` 思考中 | 紫色 | 邊框變形旋轉 | 讓我想想… |
| `worried` 擔心 | 紅色 | 紅色光暈 | 建議諮詢獸醫！ |
| `sleeping` 打盹 | — | 降低飽和度 + zZz 浮動 | zZz… |

- **互動**：點擊觸發 `vcatClick()`（切換興奮 + 愛心動畫 + 隨機對話）
- **自動行為**：每 11 秒隨機說話 + 愛心
- **心情更新**（`updateVirtualCat`）：根據 AI 回覆關鍵字自動切換心情
- **自訂照片上傳**：`vcatUploadPhoto()` 觸發 → `vcatHandleUpload(input)` 處理
  - 使用 `<label for="vcat-upload-input">` 觸發（取代舊版 `<button onclick>`）
  - FileReader 讀取 → Canvas 裁切上半部（臉部）300×300 → 壓縮為 JPEG 0.85
  - 存入 `localStorage` key `vcat_custom_photo`
  - 開機時 `initVCat()` 自動載入已儲存的照片

### 11. 中/英語言切換

- **切換機制**：`setLang('zh'|'en')`，透過底部彈出面板選擇
- **翻譯物件**（`T`）：包含 zh 和 en 兩組完整 UI 文字
  - `appName`、`newChat`、`history`、`clearData`、`hint`、`placeholder`
  - `modeLabel`（各模式的顯示名稱）
  - `welcome`（歡迎語）、`suggests`（快速操作卡片）
  - `modes`（模式面板的名稱與描述）
- **英文模式樣式**：`#paws-app.lang-en` 套用 Dancing Script 草書字型
- **變數**：`curLang`（預設 `'zh'`）

### 12. 資料持久化

**localStorage key：`pawsense_v5`**

```js
{
  users: [{
    name: '我',
    email: 'local',    // 固定本地用戶
    pw: '',
    cats: [],          // 貓咪資料陣列
    records: [],       // 分析紀錄陣列
    sessions: [],      // 對話歷史陣列
    favs: []           // 收藏（未使用）
  }],
  cur: 'local'         // 當前用戶 email
}
```

**session 結構**：
```js
{ id, title, mode, messages: [...], history: [...] }
```

**其他 localStorage keys**：
- `vcat_custom_photo`：虛擬貓咪自訂照片（base64）

### 13. 訊息發送流程 (`sendMsg`)

1. 取得輸入文字 + pending 照片
2. 建構用戶訊息物件（純文字 or 含圖片陣列）
3. 渲染用戶氣泡（含照片預覽）
4. 儲存 session
5. 顯示打字指示器
6. 路由判斷：
   - `cat` 模式或 `catAddStep` 有值 → `catFlow()`
   - 包含「新增貓咪」→ 切換至 cat 模式
   - `analyze` 模式且無貓咪 → 引導先新增貓咪
7. 呼叫 `staticAI()` 取得回覆
8. 人工延遲 600-1400ms（模擬 AI 思考）
9. 解析回覆中的壓力指數/風險等級（正則）
10. 若 analyze 模式且解析成功 → 建立結果卡片 + 存入 records
11. 否則 → 普通文字回覆
12. 更新虛擬貓咪心情

---

## 已知問題

### 1. HTML 中的 Markdown 語法殘留

- **Line 333**：`` ``` `` 反引號（開始標記）
- **Line 339**：`` ``` `` 反引號（結束標記）

這兩行出現在 `.chat-hd` 內部，包裹著 `hd-title`、`lang-btn`、`mode-chip` 三個元素。
瀏覽器會將這些反引號當作純文字渲染顯示在頁面上。

### 2. CSS 重複定義

- `#vcat-wrap` 樣式定義了兩次（line 217 和 line 248），部分屬性不同
- `#vcat` 樣式定義了兩次（line 220-222 和 line 249-251）
- `.vcat-bubble` 樣式定義了兩次（line 218 和 line 267），顏色主題不同（暗色 vs 淺色）
- `.vcat-name` 樣式定義了兩次（line 224 和 line 269）
- `.vcat-mood-badge` 樣式定義了兩次（line 225 和 line 270）

### 3. 已移除的功能（相比前一版本）

以下功能在最新版本中已被移除：

- **Portfolio 作品集頁面**：整個 `#portfolio-page` div 及其內容（導航列、Hero、About、Projects、Tools、Changelog、Contact 表單、Footer）
- **Portfolio CSS**：所有 Portfolio 相關樣式（nav、hero、about、projects、tools、log、contact、footer、scroll reveal、RWD media queries）
- **Portfolio JS**：`toggleTheme()`、`toggleMobileNav()`、`closeMobileNav()`、`updateNav()`、IntersectionObserver、`submitForm()`、scroll 事件
- **頁面切換動畫**：`showPawSense()` / `showPortfolio()` 現為空函式
- **回首頁按鈕**（`.back-home-btn`）：從聊天頂部欄移除
- **聯絡表單**：`submitForm()` 及 `max_feedback` localStorage key
- **深淺色切換**：`toggleTheme()` 及 `body.light` 主題
- **重複的 JS 定義**：Portfolio JS 和 PawSense JS 不再重複（已整合為單一份）

### 4. 配色主題變更

`:root` 變數從暗色主題改為淺藍色主題：
- 背景：`#0d0d0f`（深黑）→ `#d0e8ff`（淺藍）
- 文字：`#f0f0f0`（白）→ `#0a2040`（深藍）
- 次要色：`#aaaaaa` → `#2a4a70`
- 邊框：`rgba(255,255,255,.07)` → `rgba(30,80,160,.15)`
