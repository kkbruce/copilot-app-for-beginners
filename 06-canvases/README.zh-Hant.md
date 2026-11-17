![Chapter 06: Canvases](assets/chapter-header.svg)

> **如果你和代理程式共用一個即時的進度看板，而不是一串被埋沒的聊天紀錄，會怎麼樣？**

第 05 章介紹了 MCP 伺服器和外掛程式。現在你將使用 canvas 擴充功能，它可以透過外掛程式安裝，也可以為你自己的工作流程建立。

聊天很適合用來提問和討論。但是一旦工作階段開始進行實際工作，冗長的聊天紀錄就會變得難以瀏覽。**canvas** 是側邊面板中的共用看板，你和代理程式都可以檢視並更新。你可以使用 `/create-canvas` 加上你想要的看板描述來建立一個 canvas。

在本章中，你將先試用社群 canvas，然後建置一個工作階段看板和一個 Feature Workbench，讓計畫步驟、驗證檢查和備註保持可見。

## 學習目標

完成本章後，你將能夠：

- 說明何時 canvas 比冗長的聊天紀錄更適合
- 從外掛程式安裝並試用社群 canvas
- 使用 `/create-canvas` 建立你自己的 canvas
- 在 canvas 上保持計畫狀態和驗證證據可見

> ⏱️ **預估時間**：約 70-90 分鐘

---

## 先決條件

完成[第 05 章](../05-mcp-plugins/README.zh-Hant.md)，這樣你就知道如何安裝和檢閱外掛程式。

> [!NOTE]
> 練習 1 會安裝一個社群外掛程式。企業管理的設定可能會限制可用的外掛程式和市集。如果你無法安裝該外掛程式，請閱讀練習 1，然後繼續進行練習 2。

準備一個工作階段，並確認範例應用程式已就緒：

1. 使用 **Create from** > **Branches** > `main` 為課程儲存庫啟動新的 worktree 工作階段。選取 **Interactive** 模式。如果提示框下方出現代理程式選擇器，請選取 **Default agent**。
1. 在檢閱面板的 **Terminal** 索引標籤中，從 worktree 的儲存庫根目錄執行下列命令：

    ```bash
    cd samples/book-app-web
    npm install
    npm test -- --run
    npm run build
    ```

這三個命令都應該順利完成且沒有錯誤。請記下測試總數。你的 canvas 會把它當作基準。

---

## 來自錄音室：樂團的編曲看板

想像一個樂團正在規劃一首歌曲。他們可以在冗長的群組聊天中爭論各種選項，讓決定被埋沒，也可以使用共用的編曲看板，讓所有內容保持可見且井然有序。

![Arrangement board analogy for canvases](assets/arrangement-board-canvas.webp)

canvas 就是應用程式為人類與代理程式協作所提供的編曲看板。

---

## 核心概念

### Canvas 是共用的控制面板

canvas 是**雙向的**：你和代理程式都可以變更同一個看板。例如：

1. GitHub Copilot 將計畫步驟新增到看板。
2. 你取消勾選某個步驟，或在備註中寫下「編輯前先暫停」。
3. GitHub Copilot 會根據*你的*更新繼續進行，而不是根據被埋沒的聊天語句。

<img src="assets/human-agent-shared-surface.webp" alt="你和代理程式共用同一個 canvas。你使用 UI 控制項，代理程式使用 get、add 和 move 等動作，雙方都可以變更同一個看板。" width="800" />

<details>
<summary>練習中使用的術語</summary>

| 術語 | 意義 |
|---|---|
| Agent-callable action | canvas 提供給代理程式的動作，例如更新檢查清單項目。在練習 3 中，每個 canvas 按鈕也會要求代理程式執行工作，例如執行測試 |
| User scope | canvas 可供你跨專案使用，且不會提交到此儲存庫 |
| Local-only UI | 只會變更你所見內容、且不會告知代理程式該變更的控制項 |
| Event | 已記錄的狀態變更，例如計畫已核准 |
| Polling | 定期檢查狀態變更 |
| Timeout | 動作在停止之前可以執行的最長時間 |

你不需要在本章中撰寫擴充功能程式碼。這些術語可以幫助你理解後續練習中的詳細提示，並診斷產生的 canvas。

</details>

### 內建工作介面優先

你在先前章節中使用的 **Plan**、**Terminal**、**Browser** 和 **Changes** 面板，每個工作階段都有。canvas 則額外新增一個你針對該任務設計的介面。

### 何時使用 Canvas

對於簡短任務的快速解答，請使用聊天。當工作有需要保持可見的狀態、重複的步驟，或你和代理程式都會使用的控制項時，請使用 canvas。

<img src="assets/chat-vs-canvas.webp" alt="聊天與 canvas 工作介面的比較" width="800" />

## 練習 1：試用你的第一個 Canvas

在建置自己的 canvas 之前，先試用社群的 canvas。[Awesome GitHub Copilot][awesome-copilot] 是精選的代理程式、指引、技能和 canvas 擴充功能集合，你可以將它們安裝到 GitHub Copilot 應用程式中。[Repository Issues Kanban][issues-kanban] 是很適合首次探索的 canvas。它會將儲存庫的問題拉進看板，讓你在工作階段中分流並追蹤。

![Repository Issues Kanban preview](assets/app-repo-issues-kanban.webp)

1. 在 Awesome Copilot 網站的 [Repository Issues Kanban][issues-kanban] 頁面上，選取 **Open in Copilot app** 將它安裝到你的 GitHub Copilot 應用程式。如果瀏覽器要求開啟 GitHub Copilot 應用程式，請允許。

    - 在 **Install plugin?** 對話方塊中，選取 **Allow**。
    - 在 **Install plugin** 對話方塊中，確認名稱為 `accessibility-kanban@awesome-copilot`，然後選取 **Install**。
    - 選取 **Customize**，再選取 **Installed** 索引標籤。確認該外掛程式出現在 **Plugins** 底下，且其切換開關為開啟。

1. 回到你在先決條件中準備的 worktree 工作階段，並送出提示 `Restart canvas extensions`。這會重新載入擴充功能，並載入剛安裝的擴充功能。
1. 在檢閱面板中，選取 **+**（**Add tab**），再選取 **Canvas**，然後選取 **Repository Issues Kanban**。如果檢閱面板已隱藏，請先選取 **View** > **Toggle Panel**。請參閱[第 00 章的 View 功能表圖片](../00-setup/README.zh-Hant.md#check-your-tools-in-terminal)。套件名稱為 `accessibility-kanban`，但應用程式中顯示的名稱是 **Repository Issues Kanban**。

    ![Review panel + menu with Canvas open and Repository Issues Kanban highlighted](assets/app-open-repo-issues-canvas.webp)

1. 看板會從目前的儲存庫載入問題，並依狀態整理。
1. 在欄位之間拖曳問題，並留意 canvas 如何在不需捲動聊天紀錄的情況下保持狀態可見。當你將問題移到 **Plan** 時，canvas 會要求代理程式在背景代理程式中規劃該問題。代理程式會讀取問題和程式碼，但不會編輯檔案。計畫就緒後，卡片會移到 **Ready**。你移到 **Plan** 的每張卡片都會啟動新的代理程式執行，因此只要移動一張卡片到那裡。

花一點時間移動各個項目。這就是你將在練習 2 中延伸使用的互動模型。

## 練習 2：建立工作階段看板

從一個運作方式像共用檢查清單的小型 canvas 開始。你和代理程式都可以檢視並更新同一份功能提案、檢查清單和備註。在練習 3 加入要求代理程式執行開發任務的按鈕之前，這個練習讓你先練習這些基本互動。

<img src="assets/session-board-collaboration.webp" alt="你和代理程式更新同一個 Session Board" width="800" />

1. 在目前的工作階段中，於提示框輸入 `/` 並選取 `/create-canvas`，然後貼上此提示：

    ```text
    Create a simple, user-scoped Session Board canvas.

    Include:
    - A Feature proposal text field
    - A checklist with Plan, Implement, and Validate
    - A Notes section with Next decision and Blocker fields

    Let both the user and the agent update the checklist and notes. Keep the layout compact and beginner-readable. Do not edit repository files, run commands, or write to GitHub while creating the canvas.
    ```

1. canvas 開啟後，確認它具有提案、檢查清單和備註區段。
1. 輸入簡短的功能提案。
1. 將 **Plan** 標示為完成，並新增下一個決定。
1. 送出提示 `Summarize the current Session Board state.`，確認代理程式的回答與你的更新相符。
1. 在檢閱面板中，開啟 **Changes** 索引標籤，確認它沒有列出任何已變更的檔案。如果該索引標籤未開啟，請選取 **+**（**Add tab**），再選取 **Changes**。建立使用者範圍的 canvas 不會變更儲存庫檔案。

**預期輸出：** 功能提案、檢查清單和備註會持續顯示在看板上。你和代理程式都可以讀取並更新它們，但看板不會執行測試或編輯應用程式。

## 練習 3：建立 Feature Workbench

你將建置一個可重複使用的 canvas，用來管理新書籍應用程式功能的本機開發內部迴圈。它會讓功能提案、計畫、實作和證據連結到同一個工作階段。

<img src="assets/session-plan-validation-board.webp" alt="工作階段計畫與驗證看板" width="800" />

練習 2 展示了你和代理程式如何讀取並更新同一份提案、檢查清單和備註。現在你將加入 canvas 按鈕，要求代理程式執行開發任務並記錄結果。

> [!TIP]
> 建立 canvas 時，請使用你可用的最強大模型。第一次產生需要更多推理。canvas 可以運作之後，你可以使用較小、較快的模型進行聚焦的變更。

1. 在目前的工作階段中，於提示框輸入 `/` 並選取 `/create-canvas`，**然後**貼上下方的提示。它會建置一個具有動作按鈕、檢查清單和證據區域的五階段工作台。你不需要完整閱讀這個提示。

    ```text
    Create a reusable, user-scoped Feature Workbench canvas for the local dev inner loop in @samples/book-app-web. Simple, compact, beginner-readable. No GitHub writes, no source edits while building it.

    Every button = agent-callable action (loading/success/error state, saves agent response). No local-only UI.

    Top: horizontal progress rail = Propose -> Plan -> Baseline -> Implement -> Validate. No Approve stage. Approval happens in the chat's Plan tab. Green = evidence-backed done, neutral = current, red = failed/blocked.

    Feature proposal: text input + one Generate plan button. Switches session to plan mode, fires prompt without waiting ("Working" status), agent inspects code + writes plan as usual, presents for approval in Plan tab (never call exit_plan_mode/ask_user elsewhere). Don't render plan text on canvas. Show only a small status pill (Working/Review/Approved/Changes requested) polled from exit_plan_mode events. No edit/approve controls on the canvas itself.

    Checklist: baseline test, baseline build, implement approved plan, review diff, browser validation, screenshots (optional), final test, final build.

    Actions in order: Run baseline (test+build, record totals) -> Implement (approved plan only) -> Browser validation (start/reuse dev server, check only the proposed behavior in the app's Browser tab, never start other browsers) -> Run final checks (test+build) -> Refresh evidence. These 4 must never use plan mode/exit_plan_mode/ask_user (unattended), give each a multi-minute timeout not the ~60s default. Return the session to interactive mode when each action ends. An action is done when its evidence is recorded; never require a matching chat reply.

    Never block on tooling: screenshots are optional, count only PNG files actually saved. Browser checks the Browser tab can't run (e.g. real keyboard input) are notes, not failures. Run final checks is available after Implement even if Browser validation failed.

    Evidence: read-only, agent-posted only, no learner notes. Dense one-liners: baseline pass/fail pill, final pass/fail pill + total delta, browser summary + screenshot count, diff summary (files changed), blockers. Truncate long text. Green only if tests pass and final total >= baseline. Before any evidence: "Run a check or capture browser evidence to record it here."

    Only mark checklist/rail items from recorded evidence, never chat inference. Dense layout, no big empty textareas, no duplicate buttons, user scope.
    ```

    <details>
    <summary>用白話說明這個提示在要求什麼</summary>

    這個提示是寫給應用程式看的，不是要你背下來。它的關鍵片語意義如下。其他術語請參閱[練習中使用的術語](#a-canvas-is-a-shared-control-panel)。

    | 提示中的片語 | 意義 |
    |---|---|
    | "Every button = agent-callable action" | 每個按鈕都會要求代理程式執行實際工作（例如執行測試），而不只是變更你螢幕上的內容 |
    | "never call exit_plan_mode/ask_user elsewhere" | 計畫核准應該只在工作階段的 **Plan** 索引標籤中進行，而不是透過 canvas 快顯視窗 |
    | "polled from exit_plan_mode events" | canvas 會定期檢查計畫的核准狀態，而不需要你自行重新整理 |
    | "give each a multi-minute timeout" | 測試、建置和瀏覽器動作可能比一般快速回應花更久的時間，因此不要讓 canvas 太早放棄 |
    | "Never block on tooling" | 如果代理程式無法儲存螢幕擷取畫面或執行瀏覽器檢查，它會記錄一則備註，工作流程則繼續進行 |
    | "user scope" | canvas 會儲存在你的電腦上並可跨專案使用，不會提交到此儲存庫 |

    </details>

    <img src="assets/app-create-canvas-command.webp" alt="在提示框的自動完成中選取的 /create-canvas 技能" width="800" />

1. 代理程式完成建置後，canvas 應該會在右側面板中開啟。這可能需要約 10 分鐘。

    > [!TIP]
    > 等待時，請閱讀上方的[用白話說明這個提示在要求什麼](#exercise-3-create-a-feature-workbench)，然後展開聊天中代理程式的工具呼叫，查看它如何建置並測試 canvas。在 canvas 開啟之前，請不要在此工作階段中送出其他提示，因為新的提示可能會改變代理程式正在建置的內容。

    > **重要：** 產生的結果每次執行可能不同。請先對照下方預期的結構再繼續。不要重複重新產生整個 canvas。

    <img src="assets/app-create-canvas-screenshot.webp" alt="側邊面板中的 Feature Workbench canvas，具有五個階段、一個功能提案欄位、動作、一份檢查清單和一個空白的證據區域" width="800" />

    確認 canvas 具有：

    - 五個階段：**Propose**、**Plan**、**Baseline**、**Implement** 和 **Validate**
    - 一個功能提案欄位和一個 **Generate plan** 按鈕
    - 八個檢查清單項目
    - 用於基準、實作、瀏覽器驗證、最終檢查和證據重新整理的動作
    - 在任何動作執行之前為空白的證據區域

    如果某個部分不對，請使用相符的修復提示：

    <details>
    <summary>修復提示（請使用與問題相符的那一個）</summary>

    **缺少部分內容：**

    ```text
    Keep the current canvas. Add any missing stages, checklist items, or actions from my original request. Do not redesign parts that already work.
    ```

    **項目在沒有證據的情況下顯示為完成：**

    ```text
    Keep the current canvas. Update checklist and progress state only from recorded action evidence. Do not infer success from chat text.
    ```

    **動作在工作完成之前就停止：**

    ```text
    Keep the current canvas. Give baseline, implementation, browser validation, and final-check actions enough time to complete npm and browser work. Show loading, success, and error states.
    ```

    **動作持續停用：**

    ```text
    Keep the current canvas. Screenshots and browser checks must never block a stage or action. Count only screenshots that are saved as PNG files. Record checks that the Browser tab cannot run as notes, not failures. Let Implement run after a passing baseline, and let Run final checks run after Implement. Do not redesign parts that already work.
    ```

    **動作在工作完成後顯示錯誤，或工作階段停留在 Autopilot 模式：**

    ```text
    Keep the current canvas. Mark an action done when its evidence is recorded. Do not require a matching chat reply. Return the session to interactive mode when each action ends. Do not redesign parts that already work.
    ```

    </details>

    如果經過兩次聚焦的修復後 canvas 仍然不相符，請使用 [Markdown 備援方案](#markdown-fallback)，並手動繼續進行驗證步驟。

1. 在 **Feature proposal** 中貼上下列內容，然後選取 **Generate plan**：

    ```text
    Add a Clear filters control that resets search, genre, and reading status to their default values. Show it only when at least one filter is active.
    ```

    工作階段會切換到 **Plan** 模式，代理程式會撰寫實作計畫。在計畫準備好供檢閱之前，功能狀態會顯示 **Working**。

    ![Feature Workbench with the Plan stage current and the status Working while the session is in Plan mode](assets/app-feature-workbench-propose.webp)

1. 在 **Plan** 索引標籤中檢閱計畫。請不要選取 **Approve and implement this plan**：代理程式會在你記錄基準之前就開始變更應用程式。在聊天底部的 **Review plan** 方塊中，選取 **Exit plan mode and I will prompt myself**。如果方塊仍然開啟，請選取 **Continue**。確認模式為 **Interactive**。canvas 仍然會記錄你的核准。然後選取 **Feature Workbench** 索引標籤返回 canvas。**Plan** 階段應該已完成，且功能狀態應該顯示 **Approved**。

    ![Feature Workbench with the Propose and Plan stages complete and the status Approved](assets/app-feature-workbench-plan.webp)

1. 在 canvas 上選取 **Run baseline**，以記錄任何變更之前的應用程式狀態。此動作會在 **Autopilot** 模式下執行，讓代理程式不會停下來提問。結束時，工作階段會回到 **Interactive** 模式。在聊天中，展開此動作的工具呼叫。依據產生的 canvas 而定，它們會顯示 `npm test -- --run` 和 `npm run build` 的輸出，或是包含測試總數的檢查工具結果。確認 canvas 上的測試總數和建置結果與該輸出相符。

    ![Expanded check tool output with 4 total and 4 passed tests, next to the Feature Workbench Baseline evidence of 4/4 tests and a passing build](assets/app-feature-workbench-baseline.webp)

    **Baseline** 階段及其兩個檢查清單項目應該顯示為完成。

1. 選取 **Implement**，根據已核准的計畫建置功能。完成時，**Implement** 階段會完成，且證據中會有一列 **Diff**。選取提示框上方的 **Changes** 標籤，確認變更僅限於已核准的功能。預期會在 `samples/book-app-web` 的檔案中看到變更，例如 `src/App.tsx`、`src/components/BookFilters.tsx`、`src/styles/app.css` 和測試檔案。

    ![Feature Workbench with the Implement stage complete and a Diff row in the evidence, next to the Changes pill above the prompt box](assets/app-feature-workbench-implement.webp)

1. 選取 **Browser validation**，讓代理程式在 **Browser** 索引標籤中檢查功能。這可能需要數分鐘。然後自行在 `http://127.0.0.1:5173` 的應用程式中檢查下列狀態。代理程式通常會在名為 **Book App Web** 的 **Browser** 索引標籤中開啟它。如果沒有，請選取 **+**（**Add tab**），再選取 **Browser**，然後輸入位址。

    | 狀態 | 預期行為 |
    |---|---|
    | 沒有任何作用中的篩選 | 看不到 **Clear filters** |
    | 有作用中的搜尋詞彙 | 看得到 **Clear filters** |
    | 有作用中的類型或閱讀狀態 | 看得到 **Clear filters** |
    | 你選取 **Clear filters** | 搜尋、類型和閱讀狀態會回到預設值 |
    | 篩選已清除 | 看不到 **Clear filters**，且完整的書籍清單和統計資料會恢復 |

    ![Feature Workbench with Browser evidence recorded and Browser validation done, next to Copilot's report of the checks that passed and the checks recorded as notes](assets/app-feature-workbench-browser-validation.webp)

    完成檢查後，請停止開發伺服器。如果代理程式在背景啟動它，請選取提示框上方的 **Background** 並停止它。如果代理程式在 **Terminal** 索引標籤中啟動它，請關閉該索引標籤並選取 **Close terminal**。

1. 選取 **Run final checks**。確認測試和建置通過，且最終測試總數至少等於基準總數。如果實作新增了測試，總數可能會增加。

    ![Feature Workbench with all five stages complete, and evidence that shows 4 baseline tests and 14 final tests](assets/app-feature-workbench-validate.webp)

**預期輸出：** 五個階段全部完成，且證據顯示基準與最終檢查通過、瀏覽器結果和已變更的檔案。你的測試總數和檔案清單可能與圖片不同，因為代理程式會撰寫自己的測試。

<a id="markdown-fallback"></a>

<details>
<summary>選用：如果 canvas 無法運作的 Markdown 備援方案</summary>

### Markdown 備援方案

如果 `/create-canvas` 無法使用，或產生的 canvas 在兩次聚焦的修復後仍然無法運作，請在 Markdown 成品中保持相同的工作流程：

```text
Create a Markdown artifact named feature-workbench.md. Do not add it to the repository.

Include the Propose, Plan, Baseline, Implement, and Validate stages; the eight checklist items from my Feature Workbench request; and an Evidence section for baseline checks, changed files, browser validation, final checks, and blockers.

Update an item only after the current session produces matching terminal, diff, or browser evidence.
```

從工作階段的 **Files** 索引標籤開啟該成品。從工作階段中執行基準、實作、瀏覽器和最終檢查步驟，然後要求代理程式用你驗證過的證據更新該成品。Markdown 成品沒有動作按鈕，但它能讓相同的計畫和驗證紀錄保持可見。

</details>

<details>
<summary>canvas 背後：Canvas 檔案存放的位置</summary>

你在練習 2 和 3 中執行了 `/create-canvas`。開啟產生的檔案是選用的。

| 位置 | 範圍 | 最適合 |
|---|---|---|
| `~/.copilot/extensions` | 使用者 | 個人實驗。在課程中建議使用此位置，這樣就不會提交任何內容 |
| `.github/extensions` | 專案或團隊 | 共用的課程和團隊工作流程 |

canvas 包含一個進入點檔案，例如 `extension.mjs`。它也可以包含其他模組、HTML 檢視、用於中繼資料和相依性的 `package.json` 檔案，以及儲存其狀態的檔案。

在接受額外產生的程式碼之前請先暫停。檢查功能名稱、儲存的狀態、UI 控制項，以及是否包含任何私人資料。

如果 canvas 在編輯後無法開啟，請檢查擴充功能相依性、重新載入需求、語法錯誤，以及應用程式讀取的是使用者範圍還是專案範圍的資料夾。

</details>

---

## 疑難排解

如果某些功能未如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出了所有章節的問題和解決方法。

<details>
<summary>Canvas 問題</summary>

### 沒有開啟任何 canvas

確認你輸入了 `/create-canvas`。送出一次提示 `Restart canvas extensions`。如果命令或面板仍然缺少，請使用 [Markdown 備援方案](#markdown-fallback)。

### 社群 canvas 未列出

尋找 **Repository Issues Kanban**。`accessibility-kanban` 是套件名稱，不是應用程式顯示的名稱。

### 產生的版面配置不完整

將 canvas 與練習中預期的結構比較。然後使用與缺少部分相符的那一個修復提示。

### 計畫狀態停留在 Working

回應聊天底部的 **Review plan** 方塊。然後在 canvas 上選取 **Refresh evidence**。

### 動作按鈕持續停用

canvas 只有在前一個步驟有證據之後，才會啟用每個動作。如果 **Implement** 或 **Run final checks** 因為螢幕擷取畫面或瀏覽器檢查無法執行而持續停用，請使用練習 3 中的 **An action stays disabled** 修復提示。

### 動作顯示錯誤，但其階段已完成

有些產生的 canvas 會在代理程式記錄證據之後，顯示例如「No correlated agent response was saved」的錯誤。請使用練習 3 中的 **An action shows an error after its work is done** 修復提示。

### 工作階段停留在 Autopilot 模式

canvas 動作會在 **Autopilot** 模式下執行。如果工作階段在動作之後沒有回到 **Interactive** 模式，請在提示框中變更模式。若要修正 canvas 以便後續動作使用，請使用練習 3 中的 **An action shows an error after its work is done, or the session stays in Autopilot mode** 修復提示。

### 開啟了多個 Feature Workbench 索引標籤

代理程式在測試 canvas 時，可能會開啟額外的 canvas 副本。副本可能顯示 **Load failed**，或在 **Browser** 索引標籤中開啟。請關閉多餘的索引標籤，並保留具有 canvas 圖示且顯示你的提案和證據的索引標籤。

### 缺少 Terminal 或 Browser 索引標籤

如果檢閱面板已隱藏，請選取 **View** > **Toggle Panel**。在檢閱面板中，選取 **+**，再選取 **Terminal** 或 **Browser**。如果索引標籤仍然缺少，請將應用程式更新到最新版本。

### 看板與證據不符

將看板與此工作階段 worktree 中的終端機和瀏覽器輸出比較，而不是與範例圖片或其他工作階段比較。確認命令是在 `samples/book-app-web` 中執行的。取消勾選任何沒有輸出支持的項目，然後再次執行檢查。

### 自訂 canvas 中出現敏感資料

移除該資料並以安全的範例資料取代。如果你已擷取 canvas 的螢幕畫面，請重新擷取。

</details>

---

## 重點整理

1. canvas 為工作階段在側邊面板中提供可見、共用的看板。
2. 使用 `/create-canvas` 和簡短的描述來建立該看板。
3. 來自 [Awesome GitHub Copilot][awesome-copilot] 的社群 canvas 讓你在建置自己的 canvas 之前，先安裝並試用其互動模型。
4. 看板上的證據應該來自實際的終端機或瀏覽器輸出，而不是來自聊天推論。

---

## 作業

![Assignment](../assets/assignment.webp)

### 核心作業

在 **Propose** 之後，為 Feature Workbench 擴充一個 **Assess** 階段。該階段必須在規劃開始之前，將功能提案與應用程式目前的行為做比較。

在 `samples/book-app-web` 中挑選一項你在先前章節中尚未交付的小型、適合初學者的改善。

1. 在 **Propose** 之後新增 **Assess** 階段，以根據應用程式目前的行為評估功能提案。輸入 `/`，選取 `/create-canvas`，並送出聚焦的變更請求，就像練習 3 中的修復提示一樣。例如：

    ```text
    Keep the current Feature Workbench canvas. Add an Assess stage between Propose and Plan. Assess asks the agent to compare the feature proposal with the app's current behavior and records the result as evidence. Do not redesign parts that already work.
    ```

1. 讓工作台依序執行 assess、plan、baseline、implement 和 validate。
1. 確認每個已完成的階段都有來自目前工作階段的證據。

**核心成功準則：** canvas 顯示從評估到本機驗證、有證據支持的進度，而且你不需要重新閱讀整個聊天紀錄就能找出下一個決定。

### 選用的 GitHub 工作流程挑戰

只有在你有權限於訓練儲存庫中建立問題和提取要求時，才完成此挑戰。開始之前，請先檢閱[第 03 章的問題與提取要求工作流程](../03-development-workflows/README.zh-Hant.md)。

1. 新增為已評估的功能建立問題的動作。
1. 建立連結到該問題的提取要求。
1. 如果你可以使用 Copilot 程式碼檢閱，請要求 Copilot 檢閱。
1. 將問題、提取要求連結和檢閱狀態作為證據，在 canvas 上保持可見。

**挑戰成功準則：** canvas 顯示一個連結到該問題的開啟中提取要求。如果你要求了 Copilot 檢閱，canvas 也會顯示其狀態。在此作業中請不要合併該提取要求。

---

## 下一步

在第 07 章中，你將把可重複的提示轉換成自動化。你會建立手動的提取要求就緒報告，然後排程執行。本章最後有一個關於事件觸發程序和雲端自動化的選用章節。你不需要合併本章的功能，也不需要保持其 canvas 開啟就能繼續。

**[← 返回第 05 章](../05-mcp-plugins/README.zh-Hant.md)** | **[繼續前往第 07 章 →](../07-automations/README.zh-Hant.md)**

---

## 來源參考

- [Working with canvas extensions][canvas-extensions]
- [Customizing the GitHub Copilot app][customize-app]
- [GitHub Copilot app generally available][app-changelog]
- [GitHub Copilot app product blog][app-blog]

[canvas-extensions]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/working-with-canvas-extensions
[customize-app]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/customize-github-copilot-app
[app-changelog]: https://github.blog/changelog/2026-06-17-github-copilot-app-generally-available/
[app-blog]: https://github.blog/news-insights/product-news/github-copilot-app-the-agent-native-desktop-experience/
[awesome-copilot]: https://awesome-copilot.github.com/
[issues-kanban]: https://awesome-copilot.github.com/extension/accessibility-kanban/
