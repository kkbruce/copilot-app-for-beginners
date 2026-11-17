# Training GitHub Scenarios

第 00 章涵蓋了一般的 fork、連接及設定指令碼流程。本附錄說明[設定指令碼](../.github/scripts/setup-training-scenarios.js)會建立哪些內容，並為無法執行該指令碼的學習者提供手動備援步驟。

請使用 fork 或可隨時丟棄的訓練存放庫。請勿使用正式環境的存放庫。

## 你將建立的內容

| 課程項目 | 詳細資料 | 用於 |
|---|---|---|
| 九個標籤和五個 issue | [課程 issue 草稿](../samples/app-course-issues.zh-Hant.md) | 第 02、03 章 |
| 七個練習分支 | [分支名稱與變更](#manual-fallback-create-practice-branches) | 第 02、03 章 |
| 三個提取要求：一個含交談留言，一個含刻意失敗的檢查 | [提取要求設定](#manual-fallback-create-pull-request-scenarios)和[失敗檢查範例](#manual-fallback-create-a-failing-check-example) | 第 03、07 章 |

## 先決條件

- 一個 GitHub 帳戶，且具有在訓練存放庫中建立標籤和 issue、推送分支以及開啟提取要求的權限
- 本課程存放庫的 fork，或推送到 GitHub 的另一份可隨時丟棄的訓練複本
- 手動流程需要在存放庫的 **Settings** > **Features** 下啟用 GitHub Issues。指令碼會在需要時啟用 Issues。
- 存放庫已啟用 GitHub Actions。開啟其 **Actions** 索引標籤，如果 GitHub 顯示提示，請啟用工作流程。指令碼要求 **Book app web** 工作流程處於作用中狀態。
- 終端機中可使用 Git 和 Node.js LTS，且以已複製的 fork 根資料夾作為工作目錄
- 在執行指令碼或建立練習分支之前，工作樹必須是乾淨的，沒有未提交的變更
- 若使用指令碼流程，需要以你建立 fork 時所用的帳戶登入 GitHub CLI（`gh`）。手動步驟則使用 GitHub.com，而不是 `gh`。

如果你無法建立 issue 或提取要求，請閱讀工作流程，並改為對照螢幕擷取畫面進行學習。

## 建議流程：Fork、複製、執行設定指令碼

1. 依照[第 00 章](../00-setup/README.zh-Hant.md#connect-to-your-repository)來 fork 並連接存放庫、開啟 **Current checkout** 工作階段，並在 **Terminal** 索引標籤中檢查你的工具和 GitHub CLI 登入狀態。
2. 在已複製 fork 的根資料夾中，預覽設定指令碼將執行的動作：

   ```bash
   node .github/scripts/setup-training-scenarios.js --dry-run
   ```

3. 確認 `Repository:` 那一行顯示的是你的 fork，然後執行設定：

   ```bash
   node .github/scripts/setup-training-scenarios.js --yes
   ```

4. 完成下方的[就緒檢查](#quick-readiness-check)。

指令碼的目標是設定為本機 `origin` 遠端的存放庫。在對上游課程存放庫，或由登入使用者以外的人所擁有的存放庫進行變更之前，它會停止。對於經授權的組織 fork，只有在確認目標之後，才在 `--yes` 之外加上 `--allow-shared-repository`。

## 設定指令碼會建立的內容

在全新的訓練 fork 上，指令碼會：

- 在需要時啟用 GitHub Issues，然後擷取並更新本機預設分支。
- 建立下列九個標籤和五個 issue。每個新 issue 都會指派給透過 `gh` 驗證的 GitHub 使用者。
- 從遠端預設分支建立並推送七個練習分支。其中五個會變更應用程式行為；兩個只新增 README 備註。
- 針對預設分支開啟三個提取要求。其中一個會刻意使 **Book app web** 檢查失敗。
- 在 **Improve empty-state copy** 新增一則交談留言。這是 PR 交談中的留言，不是內嵌的檢閱討論串。
- 回到設定開始時所在的分支。

指令碼會依標題重複使用現有的 issue、依名稱重複使用分支、依來源分支重複使用提取要求，並重複使用相符的交談留言。它會更新現有課程標籤的顏色和描述。它不會重設已變更的練習分支、重新開啟已關閉的 issue 或 PR，也不會還原你已修正的失敗檢查。

## 手動備援：建立標籤

若要在不使用指令碼的情況下重現完整的課程設定，請完成下方所有手動章節。請在你的 fork 中操作，而不是上游課程存放庫。

在 GitHub.com 上你的 fork 中，開啟 **Issues** > **Labels**。使用下列名稱、顏色和描述，建立任何缺少的標籤。對於現有標籤，請更新其顏色和描述以符合下表：

| 標籤 | 顏色 | 描述 |
|---|---|---|
| `book-app-web` | `#1f883d` | Course sample app scenario |
| `good first issue` | `#7057ff` | Beginner-friendly course issue |
| `bug` | `#d73a4a` | Intentional course bug scenario |
| `tests` | `#0052cc` | Testing or CI scenario |
| `accessibility` | `#0e8a16` | Accessibility-focused scenario |
| `copy` | `#5319e7` | Content or copy scenario |
| `ui` | `#c2e0c6` | User interface scenario |
| `responsive` | `#bfd4f2` | Responsive layout scenario |
| `ci` | `#fbca04` | Continuous integration scenario |

## 手動備援：建立預先設定的 issue

在你的 fork 中建立這五個 issue。請使用每份連結草稿中的問題描述、重現步驟、預期結果和學習者目標。並附上下方分支表中對應的訓練分支名稱。請將訓練用的程式碼變更保留在分支中，而不是預設的應用程式中。

| Issue 標題與草稿 | 標籤 |
|---|---|
| [Make search case-insensitive](../samples/app-course-issues.zh-Hant.md#issue-1-make-search-case-insensitive) | `bug`, `good first issue`, `book-app-web` |
| [Keep unread stats correct when filters are active](../samples/app-course-issues.zh-Hant.md#issue-2-keep-unread-stats-correct-when-filters-are-active) | `bug`, `tests`, `book-app-web` |
| [Improve the empty state copy](../samples/app-course-issues.zh-Hant.md#issue-3-improve-the-empty-state-copy) | `accessibility`, `copy`, `book-app-web` |
| [Polish book card spacing and responsive layout](../samples/app-course-issues.zh-Hant.md#issue-4-polish-book-card-spacing-and-responsive-layout) | `ui`, `responsive`, `book-app-web` |
| [Simulate a failing stats test for CI practice](../samples/app-course-issues.zh-Hant.md#issue-5-simulate-a-failing-stats-test-for-ci-practice) | `ci`, `tests`, `book-app-web` |

將每個 issue 指派給你自己，使其出現在 **Issues** 檢視的 **Assigned to me** 索引標籤上。Issue 和 PR 的編號可能不同。請依標題而不是固定編號來比對項目。

## 手動備援：建立練習分支

建立下列所有七個分支。每個新分支都要從同一個未變更的預設分支開始，而不是從另一個練習分支開始。

下列命令假設預設分支是 `main`。如果你的 fork 使用不同的名稱，請替換成該名稱。開始之前，請在存放庫根目錄執行這些命令：

```bash
git status
git remote -v
```

確認沒有未提交的變更，且 `origin` 指向你的 fork。然後更新預設分支：

```bash
git fetch origin main
git switch main
git pull --ff-only origin main
```

| 分支 | 要進行的變更 |
|---|---|
| `practice-search-case-bug` | 套用 [Issue 1](../samples/app-course-issues.zh-Hant.md#issue-1-make-search-case-insensitive) 中區分大小寫的搜尋變更。 |
| `practice-unread-count-bug` | 將所有書籍而非篩選後的書籍傳給 `ReadingStats`，如 [Issue 2](../samples/app-course-issues.zh-Hant.md#issue-2-keep-unread-stats-correct-when-filters-are-active) 所示。 |
| `practice-empty-state-copy` | 使用 **No results** 和 **Try again.**，如 [Issue 3](../samples/app-course-issues.zh-Hant.md#issue-3-improve-the-empty-state-copy) 所示。 |
| `practice-card-polish` | 新增下方的 **Visual polish practice** README 備註。請勿變更應用程式行為。 |
| `practice-failing-stats-check` | 只計算已讀的最愛書籍，如 [Issue 5](../samples/app-course-issues.zh-Hant.md#issue-5-simulate-a-failing-stats-test-for-ci-practice) 所示。 |
| `fix-empty-state-copy` | 使用下方的空狀態 JSX。這與 `practice-empty-state-copy` 是分開的。 |
| `feature-reading-dashboard` | 新增下方的 **Training scenario note**。請勿新增儀表板功能。 |

例如，從 `origin/main` 建立第一個分支：

```bash
git switch -c practice-search-case-bug origin/main
```

只套用表格中該分支的變更。提交並推送，然後回到 `main`：

```bash
git add samples/book-app-web
git commit -m "Seed search case practice bug"
git push -u origin practice-search-case-bug
git switch main
```

對其他六個分支重複此模式，並替換分支名稱和提交訊息。請一律從 `origin/main` 建立下一個分支。如果分支已存在，請檢查並重複使用它，而不是覆寫它。

對於 `fix-empty-state-copy`，請將 `samples/book-app-web/src/App.tsx` 中的空狀態標題和段落取代為：

```tsx
<h2>No books found</h2>
<p>Adjust your filters and try again.</p>
```

對於 `practice-card-polish`，請將此文字附加到 `samples/book-app-web/README.md`：

```markdown
## Visual polish practice

Use this branch to practice planning responsive card improvements before changing UI code.
```

對於 `feature-reading-dashboard`，請將此文字附加到 `samples/book-app-web/README.md`：

```markdown
## Training scenario note

This branch is used for the Agent Merge readiness discussion in the course. It keeps the app behavior stable while giving learners a safe pull request to inspect.
```

## 手動備援：建立提取要求情境

在 GitHub.com 上，使用你已推送的分支開啟三個提取要求。將基底分支設為 `main`，或你的 fork 的預設分支。請使用這些確切的標題和連結的情境摘要：

| PR 標題 | 來源分支 | 情境 |
|---|---|---|
| Improve empty-state copy | `fix-empty-state-copy` | [PR scenario 1](../samples/app-course-pr-scenarios.zh-Hant.md#pr-scenario-1-review-comment-asks-for-clearer-empty-state-copy) |
| Failing stats check practice | `practice-failing-stats-check` | [PR scenario 2](../samples/app-course-pr-scenarios.zh-Hant.md#pr-scenario-2-failing-ci-points-to-the-stats-test) |
| Reading dashboard merge-readiness practice | `feature-reading-dashboard` | [PR scenario 3](../samples/app-course-pr-scenarios.zh-Hant.md#pr-scenario-3-agent-merge-waits-on-checks-and-review-state) |

在 **Improve empty-state copy** 上，於 PR 的 **Conversation** 索引標籤中新增此留言：

> The copy is better, but can we make it more helpful for a first-time learner? Please mention that they can change the search term, genre, or reading status.

這與指令碼的交談留言相符。請勿為此設定建立內嵌的檢閱討論串。請讓所有三個 PR 保持開啟，以供課程練習使用。

## 手動備援：建立失敗檢查範例

存放庫包含 [Book app web 工作流程](../.github/workflows/book-app-web.yml)。它使用 Node.js 24，以 `npm ci` 安裝相依套件，執行 `npm test -- --run`，並以 `npm run build` 建置 `samples/book-app-web`。

上方建立的 **Failing stats check practice** PR 就是失敗檢查範例。請勿為它另外建立分支或 PR。

1. 開啟該 PR，並等待 **Book app web** 檢查完成。
2. 確認它在 `src/tests/stats.test.tsx` 中失敗，原因是最愛數量排除了未讀的最愛書籍。
3. 為第 03 章保留這個刻意的失敗。請勿在設定期間弱化測試或修正該分支。

在全新的訓練 fork 上，另外兩個預先設定的 PR 應該會通過。如果沒有任何檢查啟動，請確認 fork 的 **Actions** 索引標籤中已啟用工作流程。

## 確保訓練存放庫安全

- 使用 fork 或可隨時丟棄的訓練存放庫。
- 每個分支只保留一個迴歸問題。
- 請勿將刻意製造迴歸的分支合併到預設分支。
- 請勿加入祕密資訊、正式環境資料或私人客戶資料。
- 如果課程結束後不再需要，請刪除訓練分支。

## 快速就緒檢查

在全新的訓練 fork 上開始第 02 章之前，請確認：

- [ ] 所有九個課程標籤都存在。
- [ ] 所有五個 issue 都存在、具有列出的標籤，並已指派給你。
- [ ] 所有七個練習分支都已推送到你的 fork。
- [ ] 所有三個 PR 都針對預設分支開啟。
- [ ] **Improve empty-state copy** 具有上方所示的交談留言。
- [ ] **Failing stats check practice** 具有預期失敗的 **Book app web** 檢查，且另外兩個 PR 通過。
- [ ] 你可以在 GitHub Copilot app 的 **Issues** 和 **Pull requests** 檢視中看到你 fork 的 issue 和 PR。

如果你已完成某個練習，其 PR 狀態或檢查結果可能已經改變。重新執行指令碼不會復原該工作。
