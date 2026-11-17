# Book App Web PR Scenarios

使用這些草稿為課程建立可重複的提取要求練習。

## PR scenario 1: Review comment asks for clearer empty-state copy

分支：`fix-empty-state-copy`

### PR 摘要

此 PR 更新了篩選條件沒有符合任何書籍時顯示的空狀態。

### 預先設定的 PR 交談留言

The copy is better, but can we make it more helpful for a first-time learner? Please mention that they can change the search term, genre, or reading status.

### 學習者目標

要求 Copilot 處理 PR 交談留言、檢查 diff，並決定是否接受該變更。

## PR scenario 2: Failing CI points to the stats test

分支：`practice-failing-stats-check`

### PR 摘要

此 PR 刻意變更統計邏輯，讓 CI 可以示範失敗的檢查。

### 預先設定的 CI 失敗

`npm test -- --run` 在 `src/tests/stats.test.tsx` 中失敗，因為最愛數量排除了未讀的最愛書籍。

存放庫包含 `.github/workflows/book-app-web.yml`，它會以 `npm ci` 安裝相依套件、執行測試，並建置 `samples/book-app-web`。在建立刻意製造迴歸的分支之後，請將此工作流程用於失敗檢查的課程。

### 學習者目標

將失敗的檢查作為脈絡，要求 Copilot 找出根本原因，並在不變更測試預期的情況下修正統計邏輯。

## PR scenario 3: Agent Merge waits on checks and review state

分支：`feature-reading-dashboard`

### PR 摘要

此 PR 新增一則安全的訓練備註，用於在測試通過且留言都已解決之後，討論合併就緒狀態。

### 合併就緒檢查清單

- 測試以 `npm test -- --run` 通過。
- 應用程式以 `npm run build` 建置成功。
- `Book app web` 工作流程在 GitHub Actions 中通過。
- 檢閱留言已解決。
- 學習者確認該分支對訓練存放庫是安全的。

### 學習者目標

討論何時適合使用 Agent Merge、它會等待什麼，以及為什麼仍應由人員檢閱最終的 diff。
