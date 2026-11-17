---
name: check-screenshots-content
description: 驗證 GitHub Copilot app 截圖與課程內容相符。在執行 /check-screenshots-content 時使用，或在被要求確認章節 assets 資料夾中以 app- 為前綴的截圖，是否與引用它們的 README 標籤、按鈕名稱、功能表項目、對話方塊選項及逐步說明一致時使用。
---

# 檢查截圖內容

使用此技能來確認課程中的 **app 截圖** 保持同步，
與描述它們的 **課程文字** 一致。它會載入每一張 app 截圖，
從每張圖片中讀出 UI 文字，並將該文字與嵌入或描述它的
章節 README 進行交叉比對。

## 此技能假設的慣例

- App 截圖（真實的 GitHub Copilot app 擷取畫面）命名為
  **`app-<topic>.webp`**，並放在章節的 **`assets/`** 資料夾中，例如
  `03-development-workflows/assets/app-workspace-panel.webp`。
- 插圖與類比藝術圖（錄音室主題、流程圖）**不會**
  使用 `app-` 前綴，因此此技能會忽略它們。

若某張明顯是 app 截圖的圖片缺少 `app-` 前綴，請標記出來，讓作者
可以重新命名（並更新 README 的圖片參考）。

## 要檢查的內容

對於每一張 `app-*` 截圖，請將 **畫面上的確切文字** 與
課程提及它的方式進行比較：

1. **標籤的大小寫與用字** — UI 標籤必須與截圖逐字相符，
   包括大小寫。例如側邊欄顯示 `Pull requests`（小寫
   「r」）、`Issues`、`Dashboards`、`Automations` 與 `Customize`。`Chats`
   列會在聊天存在之後出現在 `Sessions` 之下。請對應到特定的控制項：
   雲端 Tools 群組是 `Pull Requests`，但側邊欄檢視是
   `Pull requests`。
2. **按鈕、功能表項目與對話方塊選項** — README 告訴使用者要點選的名稱
   必須與截圖相符，例如 `Create from…`、`Toggle Panel`、
   `Exit plan mode and I will prompt myself`、`Local folder or repository`、
   `GitHub repository`、`Repository URL`。
3. **模式與控制項名稱** — `Interactive`、`Plan`、`Autopilot` 及其
   說明，以及模型／推理強度控制項。`Copilot Fusion`
   具有 `Standard`、`High` 與 `Max` 強度等級；它不是工作階段模式。
4. **圖片嵌入與替代文字** — 每一張 `app-*` 圖片至少被一份
   README 嵌入（沒有孤立的圖片），每個 `![...](...app-...)` 連結都能解析到
   存在的檔案（沒有損壞的路徑），且替代文字與圖片所顯示的內容相符。
5. **指引 ↔ 截圖涵蓋範圍** — 當 README 步驟提到截圖確實顯示的 UI
   操作介面時，用字必須相符。當某個步驟
   提到 **沒有** 任何截圖顯示的操作介面時，請將其回報為涵蓋缺口
   （這些通常已用 `<!-- app-screenshot: ... -->` 或
   `<!-- MANUAL STEP TO VERIFY ... -->` 註解標示）。

請將
[`.github/skills/github-copilot-app-automation/references/app-ui-map.md`](../github-copilot-app-automation/references/app-ui-map.zh-Hant.md)
視為 UI 標籤拼寫與大小寫的標準來源。若截圖與
UI 地圖不一致，請以截圖為準，並註明 UI 地圖需要更新。

## 工作流程

1. **探索並準備圖片。** 執行協助程式，它會列出每一張
   `app-*` 截圖，並寫出可供載入的 PNG 複本（部分 `.webp` 檔案
   無法直接在圖片檢視工具中載入）：

   ```bash
   bash .github/skills/check-screenshots-content/sample_codes/list-app-screenshots.sh
   ```

   若您偏好手動進行的備案：以 glob 比對 `*/assets/app-*`，並在檢視前轉換任何
   `.webp`，使用 `sips -s format png <file>.webp --out /tmp/<name>.png`。

2. **讀取每一張截圖。** 檢視 PNG 並轉錄可見的 UI 文字：
   標籤、按鈕說明文字、功能表項目、對話方塊標題與選項、模式名稱、
   工具提示，以及任何鍵盤快速鍵。

3. **找出課程在何處參考它。** 對於每張圖片，請在各章節中以 grep 搜尋
   其檔名，以找出嵌入的 README 及周圍的步驟，例如
   `grep -rn "app-workspace-panel" .` 然後閱讀替代文字與附近的
   編號步驟。

4. **交叉比對** 轉錄的文字與 README，使用上方
   *要檢查的內容* 中的規則。同時掃描整個課程中可見的標籤，
   以找出未嵌入該圖片的章節中大小寫的偏差
   （例如 `grep -rn "Pull Requests" .`）。

5. **回報** 發現（請見 *輸出*）。檢查過程中請勿編輯圖片或重新命名檔案。
   只有在使用者要求時，才提出或套用文字修正。

## 輸出

產生一份簡明、分組的報告：

- **✅ 相符** — 值得確認的截圖 ↔ 課程文字一致之處（簡短）。
- **⚠️ 不一致** — 具體的不符之處，每一項都包含截圖、
  預期的畫面文字、實際的課程文字，以及 `file:line` 引用。
- **📷 涵蓋缺口** — 提到沒有截圖支援之 UI 的 README 步驟，
  以及任何缺少 `app-` 前綴的明顯 app 截圖。

請保持引用精確（`path/README.md:NN`），以便輕鬆套用修正。

## 安全性與注意事項

- 請在暫存目錄（`/tmp`）中將 `.webp` 轉換為 PNG，切勿將衍生的圖片寫入
  存放庫。
- 截圖可能包含帳戶名稱、私人存放庫名稱或路徑。請勿
  將私人資料複製到報告中；請以通用方式描述 UI。
- 大小寫很重要：產品標籤使用應用程式確切的句首大寫形式
  （例如 `Pull requests`、`Assigned to me`），即使在標題式大寫的標題中也是如此。
