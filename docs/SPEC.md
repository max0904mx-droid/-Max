# PawSense AI + Portfolio — 功能規格文檔

## 專案概覽

| 項目 | 內容 |
|------|------|
| 專案名稱 | Yu Chen AI — Developer & Builder |
| 作者 | Yu Chen AI (max0904mx-droid) |
| 技術棧 | HTML / CSS / JavaScript（純前端，無框架） |
| 外部資源 | Google Fonts（Noto Serif TC、Dancing Script、Space Mono、Syne、Instrument Serif、DM Sans） |
| 資料存儲 | localStorage |
| 部署方式 | GitHub Pages (`max0904mx-droid.github.io/-Max`) |
| 檔案結構 | 單一 `index.html`（約 1870 行） |

---

## 頁面一：Portfolio 作品集 (`#portfolio-page`)

### 1. 導航列 (`<nav>`)

- **桌面版**：固定頂部、毛玻璃背景（`backdrop-filter:blur`）、Logo + 連結（Home / About / Projects / Tools / Contact）
- **手機版**（≤768px）：隱藏 nav-links，顯示漢堡選單按鈕（`.hamburger`），點擊展開 `.mobile-nav` 下拉面板
- **深淺色切換**：`toggleTheme()` 切換 `body.light` class，按鈕圖示 🌙/☀️
- **相關函式**：`toggleTheme()`、`toggleMobileNav()`、`closeMobileNav()`

### 2. Hero 區塊 (`#home`)

- 全螢幕高度（`min-height:100vh`）
- 背景裝飾：兩個漸層圓球（`.hero-orb.a` 藍色、`.hero-orb.b` 綠色），`pulse` 動畫
- 內容：eyebrow 標語、大字名稱（`Hi, I'm Yu Chen AI.`）、描述段落
- CTA 按鈕：
  - 「🐾 開始使用 PawSense AI」→ `showPawSense()`
  - 「查看專案」→ `#projects`
  - 「聯絡我」→ `#contact`
- 底部介紹卡片（`.hero-intro-box`）：目前專注的專案說明
- 進場動畫：`fadeUp` keyframe，各元素有不同 delay

### 3. About 區塊 (`#about`)

- 單一「聯絡方式」卡片（`.about-card`），包含：
  - **GitHub**：`github.com/max0904mx-droid`
  - **網站**：`max0904mx-droid.github.io/-Max`
- 無自我介紹文字、無雙欄 grid 佈局（`.about-contact` 取代原 `.about-grid`）

### 4. Projects 區塊 (`#projects`)

3 個專案卡片（`.proj-card`），各含 hover 特效：

| 專案 | 狀態 | 說明 | 操作按鈕 |
|------|------|------|---------|
| PawSense AI | LIVE | AI 貓咪行為分析平台 | 開始使用、Live、GitHub |
| 個人作品集 | LIVE | 目前網站本身 | 你在這裡、GitHub |
| Coming Soon | WIP | 下一個規劃中的專案 | 聯絡討論 |

### 5. Tools 區塊 (`#tools`)

6 個工具卡片（`.tool-card`），展示使用的技術：

| 工具 | 說明 |
|------|------|
| 🤖 Claude API | Anthropic 的 AI 模型 |
| 📄 HTML / CSS | 語意化結構與樣式設計 |
| ⚡ JavaScript | 互動邏輯、API 整合 |
| 🐙 GitHub Pages | 靜態網站部署 |
| 📱 Mobile-first | 響應式設計 |
| 🎨 Google Fonts | 字型資源 |

### 6. Changelog 更新紀錄 (`#log`)

時間軸式呈現（左側圓點 + 連線），版本歷史：

| 版本 | 日期 | 內容 |
|------|------|------|
| v1.4（最新）| 2025年3月 | 作品集首頁、About、深/淺色模式、漢堡選單、動畫、表單、Log |
| v1.3 | 2025年3月 | 中英文切換、字體優化 |
| v1.2 | 2025年3月 | 照片上傳、智能錯誤引導、SEO、無障礙 |
| v1.1 | 2025年3月 | v5 架構重建：側邊欄、四種模式、貓咪管理、分析卡片 |
| v1.0 | 2025年3月 | 初始版本上線 |

### 7. Contact 聯絡表單 (`#contact`)

- 欄位：名字、Email、留言
- 提交行為（`submitForm()`）：
  - 驗證三欄位皆不為空
  - 存入 `localStorage` key `max_feedback`（JSON 陣列，含 name/email/msg/time）
  - 清空表單、顯示 Toast 通知（「✓ 訊息已送出！」3 秒消失）
- 注意：**未發送任何郵件，僅存本地**

### 8. Footer

- 版權聲明：`© 2025 Yu Chen AI · Built with ❤️ & Claude API · Hosted on GitHub Pages`

### 9. 滾動動畫

- IntersectionObserver 監聽 `.reveal` 和 `.log-item` 元素
- 進入視窗時加上 `.visible` class（淡入 + 上移）
- 支援 delay class：`.reveal-delay-1` / `.reveal-delay-2` / `.reveal-delay-3`
- `updateNav()`：滾動時更新導航列 active 狀態
- **相關函式**：`observer`、`updateNav()`

### 10. RWD 響應式設計

- `≤768px`：隱藏桌面 nav、顯示漢堡選單、About 改單欄、縮小 padding
- `≤480px`：Projects 改單欄、Tools 改兩欄

---

## 頁面二：PawSense AI 聊天應用 (`#paws-app`)

### 1. 頁面切換機制

- `showPawSense()`：隱藏 Portfolio（淡出 + 縮小動畫）→ 顯示 PawSense（`pawsIn` 動畫）→ 鎖定 body 滾動 → 顯示虛擬貓咪
- `showPortfolio()`：隱藏 PawSense → 顯示 Portfolio → 解除滾動鎖定 → 隱藏虛擬貓咪 → 滾動至頂部
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
- 回首頁按鈕（`←`）→ `showPortfolio()`
- 中：標題（`#hd-title`，預設 "PawSense AI"）
- 右：語言切換按鈕（`#lang-btn`）+ 模式選擇按鈕（`.mode-chip`）

### 4. 聊天介面

#### 歡迎頁（`buildWelcome()`）
- 顯示於無訊息時
- Logo + 標題 + 副標題
- 4 個快速操作卡片（`.sg-grid` 2×2）：
  - 🔍 行為分析 → `我想分析貓咪的行為`
  - 🐈 新增貓咪 → `幫我新增一隻貓咪`
  - 💊 健康問題 → `我的貓咪最近食慾不振`
  - 📊 查看紀錄 → `幫我看最近的分析結果`

#### 訊息氣泡
- **用戶訊息**（`.bb.user`）：右對齊，深色背景，圓角（右下方為直角）
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
- **自訂照片上傳**（`vcatUploadPhoto`）：
  - FileReader 讀取 → Canvas 裁切上半部（臉部） → 壓縮為 JPEG
  - 存入 `localStorage` key `vcat_custom_photo`
  - 開機時自動載入已儲存的照片

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
- `max_feedback`：聯絡表單提交紀錄
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

### 1. JavaScript 重複定義

以下函式/變數在 HTML 中定義了兩次：

**Portfolio JS**（出現在 line 767-834 和 line 1805-1872）：
- `isDark` 變數
- `toggleTheme()`
- `toggleMobileNav()`
- `closeMobileNav()`
- `sections` 陣列
- `updateNav()`
- `observer`（IntersectionObserver）
- `submitForm()`
- `scroll` 事件監聽

**PawSense JS**（出現在 line 938-1751 和 line 1875-1949+）：
- `DB_KEY` 變數
- `loadDB()` / `saveDB()` / `curU()` / `clearData()`
- `toggleSB()` / `closeSB()` / `openSheet()` / `closeSheet()`
- `renderSB()` / `delSess()`
- `curSessId` / `sessMsgs` 變數
- `newChat()` / `loadSess()` / `saveSess()`

後面的定義會覆蓋前面的，功能上不影響，但增加檔案大小且容易造成維護混亂。

### 2. HTML 中的 Markdown 語法殘留

- **Line 594**：`` ``` `` 反引號（開始標記）
- **Line 648**：`` ``` `` 反引號（結束標記）

這兩行出現在 `#projects` section 的 `.projects-grid` 內，包裹著 3 個 `.proj-card`。
瀏覽器會將這些反引號當作純文字渲染顯示在頁面上。

### 3. CSS 重複定義

- `:root` 變數定義了兩次（line 19-30 為 Portfolio 淺色主題，line 216-222 為 PawSense 暗色主題），後者覆蓋前者
- `#vcat-wrap` 樣式定義了兩次（line 415 和 line 446），部分屬性不同
- `*{margin:0;padding:0;box-sizing:border-box}` 重置出現兩次（line 18 和 line 215）
- `.vcat-bubble` 樣式定義了兩次（line 416 和 line 465）
