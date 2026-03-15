# PawSense AI — 外部服務串接待辦

## 總覽

目前所有功能均為純前端實作，AI 回覆靠靜態關鍵字匹配、資料存放在 localStorage。以下列出需要後端或外部服務才能真正運作的功能。

---

## 待辦項目

### 1. AI 聊天回覆 — 串接 AI API

| 項目 | 內容 |
|------|------|
| **優先級** | 🔴 高 |
| **目前實作** | `staticAI(txt, sys, mode)` 函式，使用 `KB` 物件做關鍵字匹配，15+ 預寫回覆 |
| **問題** | 無法處理知識庫外的問題；回覆固定、無上下文理解能力 |
| **相關代碼** | `staticAI()` (line ~1554)、`KB` 物件 (line ~1511-1552)、`sendMsg()` (line ~1221) |

**串接建議**：
- **方案 A**：Claude API（推薦，專案描述已提及）
  - 需後端 proxy（Cloudflare Workers / Vercel Edge Functions）保護 API Key
  - 已有完整 system prompt 建構邏輯（`sendMsg` 中的 `sys` 變數），可直接複用
  - `chatHist` 陣列已按 Claude API 的 messages 格式組織
- **方案 B**：OpenAI API（備選）
- **預估工作量**：
  - 後端 proxy：1-2 天
  - 前端改 `sendMsg()` 中 `staticAI()` 呼叫為 fetch：0.5 天
  - 串流顯示（SSE）：1 天

---

### 2. 照片 AI 分析 — Vision API

| 項目 | 內容 |
|------|------|
| **優先級** | 🔴 高 |
| **目前實作** | 照片上傳後僅在聊天中顯示圖片，無任何 AI 分析 |
| **問題** | 照片格式已轉為 base64 + `{type:'image', source:{type:'base64',...}}`，但未傳送給任何 API |
| **相關代碼** | `onPhotoSelect()` (line ~1189)、`pendingPhotos[]`、`sendMsg()` 中圖片訊息建構 (line ~1228-1235) |

**串接建議**：
- **方案**：Claude multimodal API（已支援圖片輸入）
  - 前端已將圖片轉為 Claude API 所需的 base64 格式，幾乎可直接使用
  - 在後端 proxy 中轉發 content array（含 image + text blocks）
  - 可識別貓咪品種、表情、體態、環境，增強行為分析
- **注意**：base64 圖片體積大，需考慮 API 請求大小限制
- **預估工作量**：
  - 若 AI API 已串接，額外工作約 0.5 天（主要是 prompt engineering）
  - 需新增圖片壓縮/尺寸限制邏輯

---

### 3. 資料持久化 — 資料庫

| 項目 | 內容 |
|------|------|
| **優先級** | 🟡 中 |
| **目前實作** | 全部存 `localStorage` key `pawsense_v5`，含用戶、貓咪、紀錄、對話歷史 |
| **問題** | 換瀏覽器/清除快取即遺失；localStorage 有 5-10MB 限制；base64 照片容易爆容量 |
| **相關代碼** | `DB_KEY='pawsense_v5'`、`loadDB()`、`saveDB()`、`curU()` (line ~940-950) |

**串接建議**：
- **方案 A**：Supabase（推薦）
  - PostgreSQL + 免費額度充足
  - 表結構：`users`、`cats`、`records`、`sessions`
  - Supabase JS SDK 可直接在前端使用
- **方案 B**：Firebase Firestore
- **遷移策略**：
  1. 保留 localStorage 作為離線快取
  2. 新增同步層：本地變更 → 寫入資料庫 → 其他裝置同步
- **預估工作量**：
  - 資料庫設計 + 建表：0.5 天
  - CRUD 封裝 + 替換現有 `loadDB/saveDB`：2-3 天
  - 離線/同步邏輯：1-2 天

---

### 4. 用戶認證系統

| 項目 | 內容 |
|------|------|
| **優先級** | 🟡 中 |
| **目前實作** | 硬編碼本地用戶 `{name:'我', email:'local'}`，無登入/註冊流程 |
| **問題** | 所有人共用同一個 "local" 用戶；無法跨裝置；無法區分不同使用者 |
| **相關代碼** | `boot()` 中建立預設用戶 (line ~1742-1748)、`DB.cur='local'`、`curU()` 以 email 查找用戶 |

**串接建議**：
- **方案 A**：Supabase Auth（與資料庫搭配最佳）
  - 支援 Email/密碼、Google OAuth、GitHub OAuth
  - `curU()` 改為以 Supabase session 的 user.id 查找
- **方案 B**：Firebase Auth
- **注意**：
  - 現有 `DB.users` 陣列結構需重構為真正的多用戶模型
  - 需處理未登入狀態（訪客模式 → 登入後資料遷移）
- **預估工作量**：
  - Auth 整合（登入/註冊 UI + SDK）：2-3 天
  - 用戶資料模型重構：1 天

---

### 5. 對話歷史雲端同步

| 項目 | 內容 |
|------|------|
| **優先級** | 🟡 中 |
| **目前實作** | 對話存在 `u.sessions[]`，每個 session 含 `messages` 和 `history` 陣列，全存 localStorage |
| **問題** | 對話累積後 localStorage 容量不足；含 base64 圖片的對話會快速佔滿空間 |
| **相關代碼** | `saveSess()`、`loadSess()`、`newChat()`、`delSess()` (line ~1001-1012) |

**串接建議**：
- **方案**：隨資料庫一起遷移（Supabase / Firebase）
  - `sessions` 表：`id, user_id, title, mode, created_at, updated_at`
  - `messages` 表：`id, session_id, role, content, type, created_at`
  - 圖片內容存 Storage（見第 6 項），messages 中只存 URL
- **預估工作量**：
  - 包含在「資料持久化」工作中，額外約 1 天

---

### 6. 虛擬貓咪照片 — 圖片存儲

| 項目 | 內容 |
|------|------|
| **優先級** | 🟢 低 |
| **目前實作** | 上傳後用 Canvas 裁切壓縮為 JPEG base64，存 `localStorage` key `vcat_custom_photo` |
| **問題** | 一張照片約 50-200KB base64，佔用 localStorage 空間；聊天中的照片同樣存 base64 |
| **相關代碼** | `vcatUploadPhoto()` (line ~1664)、`initVCat()` 中的 FileReader 邏輯 (line ~1689-1726)、`onPhotoSelect()` (line ~1189) |

**串接建議**：
- **方案 A**：Supabase Storage（搭配 Supabase 資料庫最方便）
  - 上傳圖片 → 取得公開 URL → 存入資料庫
- **方案 B**：Cloudinary（免費額度 + 自動圖片最佳化）
- **方案 C**：Firebase Storage
- **預估工作量**：
  - 圖片上傳 API 封裝：0.5 天
  - 替換現有 base64 存儲邏輯：0.5 天

---

### 7. 聯絡表單 — 郵件服務

| 項目 | 內容 |
|------|------|
| **優先級** | 🟢 低 |
| **目前實作** | `submitForm()` 將表單資料存入 `localStorage` key `max_feedback`，顯示 Toast「訊息已送出」 |
| **問題** | 網站主人完全收不到訊息；用戶以為已送出但實際只存在自己瀏覽器 |
| **相關代碼** | `submitForm()` (line ~814-829、~1853-1868，重複定義) |

**串接建議**：
- **方案 A**：EmailJS（推薦，免費額度 200 封/月，純前端即可）
  - 不需後端，在前端直接呼叫 `emailjs.send()`
  - 需註冊帳號、設定 Email Template
- **方案 B**：Resend API（需後端 proxy）
- **方案 C**：Google Forms 嵌入（最簡單，但 UI 受限）
- **預估工作量**：
  - EmailJS 方案：0.5 天（註冊 + 改 submitForm）

---

### 8. 分析報告匯出 — PDF 生成

| 項目 | 內容 |
|------|------|
| **優先級** | 🟢 低 |
| **目前實作** | 無此功能 |
| **問題** | 用戶無法將分析結果匯出分享或列印 |
| **相關代碼** | `buildResCard()` (line ~1075) 已有結構化的分析結果卡片 DOM |

**串接建議**：
- **方案 A**：jsPDF + html2canvas（純前端，無需後端）
  - 將 `.res-card` DOM 轉為圖片再嵌入 PDF
  - 可加上品牌 header/footer
- **方案 B**：Puppeteer（後端生成，品質更好）
- **預估工作量**：
  - jsPDF 方案：1 天
  - 加上美化排版：額外 0.5 天

---

## 建議優先順序

```
Phase 1（核心 AI 能力）
├── 1. AI API 串接（必須先做後端 proxy）
└── 2. 照片 AI 分析（共用同一個 proxy）

Phase 2（資料基礎建設）
├── 3. 資料庫遷移
├── 4. 用戶認證
└── 5. 對話同步

Phase 3（體驗優化）
├── 6. 圖片存儲
├── 7. 聯絡表單郵件
└── 8. PDF 報告匯出
```

## 補充：程式碼整理（建議在串接前先處理）

在開始串接外部服務之前，建議先解決 SPEC.md 中記錄的已知問題：

1. **移除重複的 JS 程式碼**：Portfolio JS 和 PawSense JS 各出現兩次，保留一份即可
2. **移除 HTML 中的 Markdown 殘留**：line 594 和 648 的 ``` 標記
3. **合併重複的 CSS 定義**：`:root` 變數、`#vcat-wrap`、`.vcat-bubble` 等
4. **考慮拆分檔案**：將 CSS 和 JS 抽出為獨立檔案，方便維護
