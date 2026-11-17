# GitHub Copilot 指引

在此存放庫中工作時，請使用這些指引。

這是一個供初學者使用的 GitHub Copilot app 課程存放庫。標準範例為 `samples/book-app-web`：一個僅有前端的藏書集合，用於練習檢閱、測試、瀏覽器預覽、issue 與 pull request。

除非使用者要求變更章節內容，否則請勿編輯章節 README 檔案。請讓變更保持精簡，並使初學者易於閱讀。

## 範例應用程式

`samples/book-app-web` 是一個使用 Vite、React、TypeScript 與 Vitest 的應用程式。它沒有後端、資料庫或外部 API。請勿新增這些項目。

此 UI 讓學習者可依書名或作者搜尋書籍、依類型與閱讀狀態篩選，並檢視目前所顯示書籍的閱讀統計。

請維持現有的技術堆疊。優先使用屬性（props）清楚的小型 React 元件。請將種子資料放在 `src/data/books.ts`，並將 CSS 放在 `src/styles/app.css`。請為篩選器、區域與空狀態使用無障礙標籤。

典型檔案：

- `src/App.tsx`：篩選狀態與 `filterBooks`
- `src/components/BookCard.tsx`、`BookFilters.tsx`、`ReadingStats.tsx`
- `src/data/books.ts`：種子資料
- `src/styles/app.css`
- `src/tests/filtering.test.tsx`、`src/tests/stats.test.tsx`

除非訓練情境另有說明，否則請保留以下預設行為：

- 搜尋時比對書名與作者，且不受字母大小寫影響。
- 閱讀統計（總數、已讀、未讀、最愛、平均評分）來自篩選後目前所顯示的書籍，而非未篩選的清單。
- 最愛數量包含未讀的最愛。
- 空狀態文字應提及變更搜尋詞彙、類型或閱讀狀態。

請使用目前的 Node.js LTS。此應用程式將 `engines.node` 宣告為 `>=24.15.0`。CI 使用 Node 24。

## 驗證

請一律在 `samples/book-app-web` 中作業。若缺少 `node_modules`（包括在新的 worktree 中），請先執行 `npm install`。

```bash
npm install
npm test -- --run
npm run build
npm run dev -- --host 127.0.0.1 --port 5173
```

- `npm test -- --run` 會執行 Vitest 一次後結束。請透過 npm 傳遞 `--run`，以免 Vitest 停留在監看模式。
- `npm run build` 會以 `tsc --noEmit` 進行型別檢查，然後以 Vite 建置。
- 瀏覽器預覽網址為 `http://127.0.0.1:5173`。請在該處確認篩選、統計與空狀態的行為，而不只是在聊天中確認。

此範例的 CI 為 `.github/workflows/book-app-web.yml`（`Book app web`）。它會在涉及此範例的 pull request 上執行 `npm ci`、`npm test -- --run` 與 `npm run build`。

## 課程情境

`main` 上的預設應用程式應通過測試並成功建置。

請將刻意設計的失敗行為保留在訓練分支，或 `samples/app-course-issues.md` 與 `samples/app-course-pr-scenarios.md` 中，而不要放在預設應用程式裡。

修正某個情境時，請優先新增或保留一個聚焦的測試，再變更應用程式邏輯。請勿為了讓 CI 通過而削弱失敗的測試。
