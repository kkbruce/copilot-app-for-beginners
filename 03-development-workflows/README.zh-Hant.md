![第 03 章：開發與 GitHub 工作流程](assets/chapter-header.svg)

> **如果一個受監督的工作流程，能把變更從第一次編輯一路帶到可合併的提取要求呢？**

前幾章介紹了 app、專案工作階段、工作樹與脈絡。本章將它們結合成兩個相連的迴圈：

- **內迴圈**發生在你的電腦上：理解任務、變更程式碼、檢查差異、執行測試，並預覽結果。
- **外迴圈**發生在 GitHub 上：從 issue 開始、開啟提取要求、回應檢閱意見回饋，並確認檢查通過。

GitHub Copilot app 連結了這兩個迴圈，但檢閱證據並決定什麼可以上線，仍是你的責任。

## 學習目標

完成本章後，你將能夠：

- 使用檢閱面板、測試、建置輸出與瀏覽器預覽來驗證本機變更
- 使用 Run 按鈕直接執行 Copilot 回應中的程式碼片段，或將其複製並貼到終端機
- 新增迴歸測試，並請 rubber duck 代理程式評論你的工作
- 使用 **Issues** 與 **Pull requests** 檢視，從 issue 進展到提取要求
- 請 GitHub Copilot 處理檢閱意見與失敗的檢查
- 說明人為判斷在兩個迴圈中的定位

> ⏱️ **預估時間**：約 75 分鐘

## 先決條件

完成第 [00](../00-setup/README.zh-Hant.md)、[01](../01-tour-the-app/README.zh-Hant.md) 與 [02](../02-sessions-worktrees-context/README.zh-Hant.md) 章。

本章使用由 [第 00 章設定指令碼](../00-setup/README.zh-Hant.md#seed-the-repository)所建立的練習分支、issue 與提取要求。如果你略過了該步驟，請現在執行該指令碼。如果你無法建立提取要求，仍可閱讀外迴圈的步驟並對照螢幕擷取畫面操作。

## 來自錄音室：從錄音到最終混音

音樂家不會因為第一次錄音已經完成就保留它。他們會錄音、聆聽、調整，然後再錄一次。這就是**內迴圈**：進行變更，然後檢查證據。

<img src="assets/record-playback-retake-loop.webp" alt="以錄音、回放與重錄比喻開發迴圈" width="600" />

完成的錄音仍需要經過製作流程。製作人會追蹤需求說明、回饋、品質檢查與最終核准。這就是**外迴圈**：使用 issue 與提取要求，讓驗證過的工作在 GitHub 上流轉。

<img src="assets/producer-review-desk-github.webp" alt="以製作人檢閱桌比喻 GitHub issue 與提取要求" width="600" />

## 核心概念：相信證據

Copilot 充滿自信的回應，並不等同於已驗證的軟體。請使用這些介面來判斷變更是否已準備好：

| 證據 | 在哪裡找到 | 要檢查什麼 |
|---|---|---|
| 差異 | 檢閱面板的 **Changes** 索引標籤 | 變更聚焦且容易理解 |
| 測試與建置 | 檢閱面板的 **Terminal** 索引標籤 | 相關測試與建置順利完成 |
| 執行中的 app | 檢閱面板的 **Browser** 索引標籤 | 行為如預期運作（針對 Web app 而言） |
| GitHub 檢查 | **Pull requests** 檢視中的提取要求 | 持續整合（CI）與你的本機結果一致 |

<img src="assets/development-loop.webp" alt="開發迴圈。你電腦上的內迴圈：理解、變更、檢查、測試與建置，以及預覽。GitHub 上的外迴圈：issue、提取要求、回饋、檢查與合併。人為判斷適用於兩個迴圈。" width="600" />

人為判斷適用於兩個迴圈。在內迴圈中，由你判斷差異、測試、建置與預覽是否顯示變更已準備好。在外迴圈中，由你檢閱提取要求、其意見與檢查，並決定何時可以合併。

## 確認範例 App 已準備就緒

存放庫包含 `.github/copilot-instructions.md`，它為 Copilot 提供專案專屬的指南。它描述了範例 app、其對初學者友善的慣例，以及驗證命令。

<details>
<summary>選用：Copilot 如何取得脈絡</summary>

Copilot 不只依據你的提示運作。它也會使用設定、指引與存放庫檔案。更多脈絡並不一定更好，所以請讓每一層都保持聚焦。

<img src="assets/context-layers.webp" alt="四個脈絡層：提示、設定、指引與存放庫檔案。讓每一層都保持聚焦。" width="600" />

| 脈絡層 | 包含內容 |
|---|---|
| 提示 | 你在目前回合所詢問的內容，包括以 `@` 與 `#` 附加的檔案、資料夾、issue 或提取要求 |
| 設定 | 適用於每個工作階段的 app 指引，以及單一專案的指引 |
| 指引 | 共用的存放庫指南，例如 `.github/copilot-instructions.md` 或 `AGENTS.md` |
| 存放庫檔案 | Copilot 可以存取的程式碼、測試、文件與其他檔案 |

只附加對目前任務有幫助的脈絡。聚焦的脈絡能讓 Copilot 更容易理解你的目標並遵循存放庫的慣例。

</details>

1. 在側邊欄中指向 `copilot-app-for-beginners` 專案並選取 **+**。確認提示方塊下方的工作區選取器顯示 **New worktree**。如果沒有，請開啟選取器並選擇 **New worktree**。
1. 將模式設為 **Interactive**，然後送出：

   ```text
   Summarize the @samples/book-app-web test framework and build tool that are used to validate changes. Include the commands to run tests and build the app.
   ```

1. 選取 **View** > **Toggle Panel**。請參閱[第 00 章的 View 功能表圖片](../00-setup/README.zh-Hant.md#check-your-tools-in-terminal)。
1. 選取 **Terminal**。如果它尚未開啟，請選取 **+**，然後選取 **Terminal**。

1. 執行：

   ```bash
   cd samples/book-app-web
   npm install
   npm test -- --run
   ```

   **Terminal** 應該會顯示四個通過的測試：

   ![檢閱面板開啟 Terminal 索引標籤，顯示四個通過的測試](assets/app-workspace-panel.webp)

1. 執行：

   ```bash
   npm run build
   ```

在你進行任何變更之前，測試與建置都應該通過。這會給你一個已知良好的基準。

> [!NOTE]
> 每個練習分支都會在獨立的工作樹中開啟。第一次使用新的工作樹時，請執行 `npm install`，因為相依性不會自動共用。

### 在終端機中執行程式碼片段

當 Copilot 在對話中提供程式碼片段時，你有兩種方式可以執行它們：

1. **複製並貼到終端機** - 從 Copilot 的回應複製程式碼，並貼到檢閱面板的 Terminal 索引標籤，就像上面所做的一樣。
2. **直接從回應執行** - Copilot 的回應可能在 Bash 或 PowerShell 程式碼片段旁包含 **Run** 按鈕。按一下它即可在工作階段的終端機中直接執行該片段，不必複製貼上。終端機會在可用時重複使用現有的殼層，因此環境狀態與工作目錄會在多次執行之間延續。

當你想快速驗證命令而不用手動輸入時，這種直接執行很有用，尤其是較長或較複雜的片段。

## 內迴圈：在你的電腦上開發與驗證

內迴圈如下：

1. 理解任務。
2. 進行聚焦的變更。
3. 檢查差異。
4. 執行測試與建置。
5. 預覽行為。
6. 如果證據顯示有問題，就重複上述步驟。

### 1. 檢閱並修正錯誤

`practice-unread-count-bug` 分支包含一個迴歸問題：閱讀統計資料會忽略作用中的篩選條件。

在第 02 章中，你附加了 issue 來練習提供 Copilot 聚焦的脈絡。在這裡，你將以同樣的技能作為完整內迴圈的起點：規劃修正、變更程式碼、檢查差異、執行測試，並預覽結果。

1. 選取課程專案旁的 **Create from**。
1. 選擇 **Branches**，然後選取 `practice-unread-count-bug`。app 會在新的工作樹中啟動工作階段。
1. 在提示方塊中輸入 `#2`，然後選取 **Keep unread stats correct when filters are active** 以附加種子 issue。如果你的 issue 編號不同，請輸入 `#` 並依標題選取。
1. 將模式設為 **Plan**，在附加的 issue 之後加入下列指引，然後送出：

   ```text
   Use the attached issue as the source of truth. Review @samples/book-app-web/src, identify the root cause, and create a short fix and validation plan.
   ```

1. 檢閱計畫。它應該將顯示的統計資料與套用篩選條件後目前顯示的書籍連結起來。
1. 選取 **Exit plan mode and I will prompt myself**。如果 **Review plan** 方塊仍然開啟，請選取 **Continue**。確認模式為 **Interactive**。送出：

   ```text
   Implement the plan. Keep the fix small, explain the root cause, and run the relevant tests.
   ```

1. 開啟 **Changes** 索引標籤並檢查差異。確認變更聚焦於篩選後書籍的資料流程。如果不是，請在繼續之前要求 Copilot 修訂。

   > [!TIP]
   > 如果代理程式變更了你不想變更的檔案，你可以直接在差異檢視中捨棄該檔案的變更。請在 **Changes** 索引標籤中尋找捨棄動作。在檔案還原之前會出現確認對話方塊，讓你可以先檢視這個決定。

#### 驗證修正

1. 檢閱工作階段 **Terminal** 索引標籤中的輸出。如果 Copilot 在修正後已經執行過這些命令，就不需要再執行一次。否則，請執行：

   ```bash
   cd samples/book-app-web
   npm install
   npm test -- --run
   npm run build
   ```

1. 啟動開發伺服器：

   ```bash
   npm run dev
   ```

1. 在檢閱面板中，選取 **+**，然後選取 **Browser**（如果尚未開啟）。輸入 **Terminal** 索引標籤中顯示的 **Local** URL，然後按 `Enter`。

1. 讓搜尋欄位保持空白，將類型篩選設為 **Fantasy**，並將閱讀狀態篩選設為 **Unread**。確認只出現 **The Night Circus**，且未讀數量為 **1**。

   在原本的錯誤下，未讀數量會停留在 **5**，因為統計資料使用的是完整的書籍清單。只選取 **Unread** 並不會暴露這個問題：五本未讀書籍仍然都會顯示。

1. 回到 **Terminal** 並按 `Ctrl+C` 以停止開發伺服器。在 macOS、Windows 與 Linux 上，此快速鍵都是 Control+C。

你已完成一次內迴圈週期：規劃、變更、檢查、測試、建置與預覽。

### 2. 新增迴歸測試

迴歸測試證明錯誤在修正之前會失敗，而在修正之後會通過。

1. 留在同一個 **Interactive** 工作階段。
1. 送出：

   ```text
   Add or update a focused test that renders the Book App, selects Fantasy and Unread with an empty search field, and confirms that only The Night Circus appears and the displayed unread count is 1. The test should fail with the original bug and pass with the fix.
   ```

1. 在 **Changes** 索引標籤中檢查測試變更。確認它檢查的是可見的篩選結果，而不是削弱既有的預期。
1. 執行：

   ```bash
   npm test -- --run
   npm run build
   ```

兩個命令都應該順利完成。

<details>
<summary>選用：在測試保護下重構</summary>

`samples/book-app-web/src/App.tsx` 中的 `filterBooks` 函式結合了多項檢查。在測試通過的情況下，你可以練習不改變行為的重構：

```text
Refactor filterBooks in @samples/book-app-web/src/App.tsx to extract the search, genre, and status checks into small, clearly named helper functions. Keep its signature and behavior unchanged, then run the tests.
```

檢查差異並重新執行測試。如果測試失敗，請調整實作，而不是更改預期的行為。

</details>

### 3. 徵求第二意見

rubber duck 代理程式扮演建設性的評論者。它能在你開啟提取要求之前，找出缺少的證據或假設。

1. 留在包含你的修正與測試的工作階段中。
1. 輸入 `/` 以開啟斜線命令面板，然後送出：

   ```text
   /rubber-duck Critique this session's diff, tests, and browser validation. What should I double-check before creating a pull request?
   ```

1. 將評論與你蒐集的證據比較。在繼續之前，先處理任何相關的缺口。

> [!NOTE]
> rubber duck 代理程式使用與你工作階段模型不同的 AI 模型來評論工作。只有在有合適的評論者模型可用時，Copilot 才會使用它。如果 `/rubber-duck` 無法使用，請為工作階段選擇不同的模型，或不使用斜線命令送出相同的要求。

<details>
<summary>選用：對 UI 變更使用 Pick &amp; Polish</summary>

**Pick & Polish** 讓你在 app 的瀏覽器預覽中選取一個元素，並將它直接附加到下一個提示。Copilot 會收到所選元素作為脈絡，因此你可以要求聚焦的 UI 變更，而不必描述該元素在程式碼中的位置。

1. 選取課程專案旁的 **Create from**，選擇 **Branches**，然後選取 `practice-card-polish`。
1. 在工作階段的 **Terminal** 索引標籤中，執行：

   ```bash
   cd samples/book-app-web
   npm install
   npm run build
   npm run dev
   ```

1. 在檢閱面板中，選取 **+**，然後選取 **Browser**（如果尚未開啟）。輸入 **Terminal** 索引標籤中顯示的 **Local** URL，然後按 `Enter`。
1. 在瀏覽器工具列中，選取 **Pick & Polish**。這是下圖中的步驟 1。
1. 將指標移到 app 上。當你在頁面上移動時，Pick & Polish 會醒目提示元素。
1. 選取其中一張書籍卡片的空白背景區域，使整張卡片都被醒目提示（圖中的步驟 2）。
1. 提示方塊中會出現 `article.book-card` 附件（圖中的步驟 3），且 Pick & Polish 會關閉。

   ![Pick & Polish，提示方塊中有 article.book-card 附件](assets/app-pick-and-polish.webp)

1. 在元素仍附加的情況下，送出：

   ```text
   Improve this book card's spacing and visual hierarchy while keeping the existing design and accessible text. Make the smallest useful change.
   ```

1. 預覽結果。如果你想要另一項聚焦的變更，請再次選取 **Pick & Polish**，選擇元素，並送出另一個簡短的指引。
1. 檢查差異。請 Copilot 修訂或移除任何不屬於聚焦卡片變更的內容。
1. 回到 **Terminal** 並按 `Ctrl+C` 以停止開發伺服器，然後執行 `npm test -- --run` 與 `npm run build`。

提示方塊中的附件是此工作流程的重點。它顯示 Copilot 收到的是來自已轉譯頁面的脈絡，而不只是你的文字描述。視覺變更仍可能影響行為與協助工具，因此請以與任何其他變更相同的差異、測試、建置與瀏覽器證據作為收尾。

</details>

## 外迴圈：讓工作在 GitHub 上流轉

外迴圈如下：

1. 從 GitHub issue 開始。
2. 在工作階段中實作並驗證變更。
3. 檢閱差異與證據。
4. 開啟並檢閱提取要求。
5. 回應回饋與失敗的檢查。
6. 只有在差異、本機證據與 GitHub 檢查一致時才合併。

![從 issue 到合併的工作流程：issue、工作階段、差異、核准閘道、提取要求、檢查、核准閘道與合併。回饋或失敗的檢查會回到工作階段。](assets/issue-to-merged-pr.webp)

此圖顯示通往合併的主要路徑。回饋或失敗的檢查可能會把工作送回工作階段，進行另一輪聚焦的變更與驗證週期。

### 4. 在 Issues 與 Pull requests 中尋找工作

側邊欄中的 **Issues** 與 **Pull requests** 檢視會列出與你相關的 GitHub 工作。每個檢視都有索引標籤（例如 **Assigned to me**），以及可將結果限制在單一存放庫的篩選器。

1. 在側邊欄中選取 **Issues**，並停留在 **Assigned to me** 索引標籤。
1. 選取索引標籤列右端的篩選圖示（標註 1）。選取以 `<>` 表示的 **Query filter mode** 控制項（標註 2），然後將 `repo:YOUR-OWNER/copilot-app-for-beginners` 加入現有的查詢（標註 3）。請將 `YOUR-OWNER` 取代為擁有你分叉的使用者名稱或組織。保留現有的 `state:open assignee:@me` 項目，然後按 `Enter`。

   <img src="assets/app-filter-repo.webp" alt="Issues 查詢篩選器，標註篩選圖示、Query filter mode，以及包含訓練分叉存放庫項目的查詢欄位" width="800" />

   檢視現在只會列出來自你分叉的練習 issue。

   ![已篩選為課程存放庫的 Issues 檢視](assets/app-issues-filtered.webp)

1. 在側邊欄中選取 **Pull requests**，然後選取 **Authored by me**。開啟其篩選器，選取 **Query filter mode**，並比照 issues 檢視，將相同的 `repo:` 值加入其現有的查詢。保留 `state:open author:@me`，然後按 `Enter`。練習用的提取要求會出現在此索引標籤。

> [!TIP]
> 篩選器屬於所選的檢視。當你切換索引標籤時，請檢查查詢，必要時再次加入 `repo:` 項目。

確認開啟項目時會顯示其標題、存放庫、編號與目前狀態。如果找不到預期的項目，請檢查所選的索引標籤與存放庫、你的存放庫存取權，以及組織原則。

### 5. 從 Issue 開始並開啟提取要求

Issue 1 要求你讓 book app 中的搜尋不區分大小寫。這個刻意設計的錯誤只存在於 `practice-search-case-bug` 分支。你會像第 02 章一樣使用 `#` 附加該 issue，而這次要以提取要求收尾。

1. 在 **Issues** 中，開啟 **Make search case-insensitive** issue 並檢閱詳細資料。如有需要，請在 [`samples/app-course-issues.md`](../samples/app-course-issues.zh-Hant.md#issue-1-make-search-case-insensitive) 中檢閱此情境。

   ![在 Issues 檢視中開啟的 Make search case-insensitive issue](assets/app-issue-new-session.webp)

1. 從練習分支啟動工作階段，讓錯誤存在。issue 的 **New session** 按鈕會從 `main` 啟動，而該處的搜尋已經正常運作，因此請改為選取課程專案旁的 **Create from**。
1. 選擇 **Branches**，然後選取 `practice-search-case-bug`。
1. 在提示方塊中輸入 `#1`，然後選取 **Make search case-insensitive** 以附加 issue。如果你的 issue 編號不同，請輸入 `#` 並依標題選取。
1. 將模式設為 **Plan**，在附加的 issue 之後加入下列提示，然後送出：

   ```text
   Use the attached issue as the source of truth. Create a small implementation and validation plan. Name the files you expect to change and the tests or browser checks that should prove the fix. Don't edit files yet.
   ```

1. 檢閱計畫並選取 **Exit plan mode and I will prompt myself**。如果 **Review plan** 方塊仍然開啟，請選取 **Continue**。確認模式為 **Interactive**。送出：

   ```text
   Implement the plan. Include a focused regression test that proves uppercase and lowercase searches return the same result.
   ```

1. 依循內迴圈：
   - 在 **Changes** 索引標籤中檢查差異。
   - 在 **Terminal** 中，切換到 `samples/book-app-web` 並執行 `npm install`。
   - 執行 `npm test -- --run` 與 `npm run build`。
   - 以 `npm run dev` 啟動 app。
   - 開啟 **Browser** 索引標籤，並確認如 `hobbit` 與 `HOBBIT` 的搜尋會傳回相同的結果。
   - 回到 **Terminal** 並按 `Ctrl+C` 以停止開發伺服器。
1. 確認 **Changes** 索引標籤只包含聚焦的搜尋修正及其測試。
1. 請 Copilot 協助撰寫描述：

   ```text
   Draft a pull request summary for this session. Include what changed, why it changed, and the validation performed. Don't claim a check passed unless it appears in the terminal or CI output.
   ```

1. 檢閱草稿，然後選取工作階段頂端的 **Create PR**。
1. 提取要求建立後，選取提示方塊上方的它以檢視其詳細資料。
1. 你也可以在 **Pull requests** 檢視中找到該提取要求。

請不要合併這個練習用的提取要求。正確的搜尋行為已經在 `main` 上了。

你的本機證據應該顯示行為運作正常。現在，提取要求讓檢閱者與 CI 有機會在 GitHub 上檢查同一項變更。

### 6. 處理檢閱意見與失敗的檢查

設定指令碼為此練習建立了兩個獨立的提取要求。它們不是你剛剛開啟的搜尋修正提取要求。

在側邊欄中選取 **Pull requests**，然後選取 **Authored by me**。檢查查詢是否仍包含你分叉的 `repo:` 項目。練習用的提取要求會出現在此索引標籤。

#### 回應檢閱意見

1. 開啟標題為 **Improve empty-state copy** 的提取要求。
1. 找到要求提供更實用指引的對話意見。

   ![包含空白狀態文案意見的提取要求對話](assets/app-pr-review-comment.webp)

1. 選取提取要求頂端的 **New session**，為該提取要求啟動工作階段。
1. 詢問 Copilot：

   ```text
   Address this PR conversation comment with the smallest useful change. Show me the diff and validation plan before I accept the fix.
   ```

1. 檢查差異，並確認修訂後的訊息處理了完整的意見。
1. 請 Copilot 提交並推送變更，然後回到提取要求並確認更新。

#### 修正失敗的檢查

**CI 檢查**是在提取要求上執行的自動化驗證，通常透過 GitHub Actions 執行。

1. 開啟標題為 **Failing stats check practice** 的提取要求，並檢閱失敗的 **Book app web** 檢查。

   ![Failing stats check practice 提取要求，顯示失敗的 Book app web 驗證檢查](assets/app-pr-failing-check.webp)

1. 選取提取要求頂端的 **New session**，然後送出下方的提示。新工作階段的 **PR** 索引標籤也有 **Fix failing checks** 按鈕，但此練習使用你自己的提示，讓你能看見並掌控每個步驟。

   ```text
   Analyze the failing check. Explain the root cause, identify the likely file in samples/book-app-web, and propose the smallest fix. Don't weaken the failing test.
   ```

1. 檢查差異。確認修正恢復了已讀與未讀最愛書籍的最愛計數，且沒有更動測試。

1. 在 **Terminal** 中，於 `samples/book-app-web` 執行 `npm install`、`npm test -- --run` 與 `npm run build`。

1. 啟動 `npm run dev`，並在 **Browser** 索引標籤中確認最愛數量同時包含已讀與未讀的最愛書籍。然後按 `Ctrl+C` 以停止伺服器。

1. 請 Copilot 提交並推送變更。

1. 回到提取要求，確認 **Book app web** 檢查重新執行並成功。

在差異聚焦、且本機與 GitHub 的證據一致之前，請不要將提取要求標示為就緒。

<details>
<summary>選用：Agent Merge</summary>

[Agent Merge](https://docs.github.com/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests#merging-a-pull-request) 可協助帶領提取要求通過檢閱意見、檢查與合併需求。若要找到它，請選取提取要求頂端的合併就緒按鈕，例如 **Ready to merge**。**Merge pull request** 面板會開啟，並附有 **Agent merge** 切換開關。Agent Merge 會在背景執行，可在 app 重新啟動後繼續，且只有在 GitHub 允許時才會合併。

雖然你在本課程中不會使用 Agent Merge，但在下列情況下它可能適用：

- 提取要求小且範圍明確。
- 你了解並檢閱過差異。
- 必要的檢查有意義且已通過。
- 已了解檢閱意見與存放庫的合併規則。
- 你的組織允許此功能。

除非你的團隊明確核准該工作流程，否則請勿將它用於涉及密碼、驗證、權限、計費、生產資料或部署邏輯的變更。

Agent Merge 是收尾的輔助工具，而不是人工檢閱的替代品。

![人為判斷始終在迴圈中](assets/human-judgment-loop.webp)

</details>

---

## 疑難排解

如果某些功能未如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出所有章節的問題與修正方式。

<details>
<summary>開發與 GitHub 工作流程問題</summary>

### 瀏覽器預覽沒有更新

確認開發伺服器正在目前工作階段的工作樹中執行，且 **Browser** 索引標籤使用的是 **Terminal** 索引標籤中顯示的 **Local** URL。

### 測試只在某一個工作階段失敗

確認該工作樹中已安裝相依性，且工作階段使用的是預期的練習分支。

### 我看不到 issue 或提取要求

請檢查 **Issues** 或 **Pull requests** 檢視中所選的索引標籤與查詢的 `repo:` 項目，然後檢查存放庫存取權、權限與組織原則。

### 本機結果與 CI 不一致

比較 Node.js 版本、相依性安裝、分支內容、環境變數與產生的檔案。

### 提取要求仍然被封鎖

確認必要的檢查已重新執行、檢閱意見已解決，且分支保護或合併規則並未在等待另一次核准。

</details>

---

## 重點摘要

1. 內迴圈發生在你的電腦上：理解、變更、檢查、測試、建置與預覽。
2. 外迴圈發生在 GitHub 上：issue、提取要求、回饋、檢查與合併。
3. 檢閱面板讓本機差異、終端機輸出與瀏覽器預覽保持可見。
4. 測試保護行為，並讓重構更安全。
5. **Issues** 與 **Pull requests** 檢視將 GitHub 工作連結到專案工作階段。
6. 只有在差異、本機驗證、GitHub 檢查與人工檢閱一致時，變更才算準備好。

## 作業

![作業](../assets/assignment.webp)

在一項小型 UI 任務上執行兩個迴圈：

1. 選擇一項任務：
   - 如果你略過了 **Pick & Polish**，請開啟 Issue 4，**Polish book card spacing and responsive layout**。然後使用 **Create from** > **Branches**，在 `practice-card-polish` 分支上啟動工作階段。
   - 如果你已完成它，請從 `main` 啟動新的工作階段，改為改善篩選列的標籤或輔助文字。
1. 使用 **Plan** 模式定義一項小型 UI 改進，然後切換到 **Interactive** 來實作。
1. 檢查差異，並確認它仍聚焦於所選的 UI 區域。
1. 執行 `npm test -- --run` 與 `npm run build`。
1. 以寬與窄兩種寬度預覽 app。若要檢查窄寬度，請拖曳檢閱面板的左邊緣，讓 **Browser** 索引標籤變窄。
1. 開啟提取要求，其摘要只回報你實際執行的驗證。
1. 在 **Pull requests** 檢視中檢閱該提取要求，並確認其檢查通過。

**成功準則：**你的提取要求包含一項聚焦的 UI 變更，其摘要只列出你執行過的檢查，且 **Book app web** 檢查通過。

## 下一步

在下一章中，你將搭配檢閱技能重複使用此工作流程，並建立一個只說明 Book App 而不編輯它的自訂代理程式。MCP 伺服器與外掛程式將在第 05 章介紹。

**[← 返回第 02 章](../02-sessions-worktrees-context/README.zh-Hant.md)** | **[繼續前往第 04 章 →](../04-skills-custom-agents/README.zh-Hant.md)**

---

## 來源參考

- [Working with agent sessions][agent-sessions]
- [Managing issues and pull requests][issues-prs]
- [About the rubber duck agent][rubber-duck]
- [GitHub Copilot app changelog][app-changelog]
- [GitHub Copilot app product blog][app-blog]

[agent-sessions]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions
[issues-prs]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests
[rubber-duck]: https://docs.github.com/en/copilot/concepts/agents/copilot-cli/rubber-duck
[app-changelog]: https://github.com/github/app/blob/main/changelog.md
[app-blog]: https://github.blog/news-insights/product-news/github-copilot-app-the-agent-native-desktop-experience/
