![Chapter 07: Automations](assets/chapter-header.svg)

> **如果你每天早上執行的提示可以自行執行，會怎麼樣？**

第 06 章使用 canvas 讓工作階段的工作變得可見。本章則讓可重複的工作能夠重複使用。

自動化可讓你在 GitHub Copilot 應用程式中儲存代理程式任務，並依需求執行，或稍後依排程執行。你會先從只在你選擇時才執行的手動檢閱就緒報告開始，然後排程它，讓摘要在每天早上等著你。本章最後有一個關於事件觸發程序和雲端自動化的選用章節。

## 學習目標

完成本章後，你將能夠：

- 說明何時應將重複性的代理程式工作自動化，而不是啟動手動工作階段
- 建立並測試依需求執行的本機自動化
- 排程自動化並檢閱其執行歷程記錄
- 選擇性地辨識事件觸發程序（例如 **Issue** 和 **Pull request**）以及它們適用的時機
- 選擇性地說明雲端自動化需要什麼，以及如何限制其工具

> ⏱️ **預估時間**：約 50 分鐘

---

## 先決條件

完成[第 06 章](../06-canvases/README.zh-Hant.md)和前面的章節。你將重複使用第 03 章的 **Pull requests** 檢視，以及第 04 章和第 05 章的工具存取原則。本章不需要保持 canvas 開啟，也不需要啟用 MCP 伺服器或外掛程式。

如果你先前略過了設定指令碼，請在第一個自動化練習之前，[立即執行它](../00-setup/README.zh-Hant.md#seed-the-repository)。檢閱就緒報告需要有開啟中的提取要求可供檢查。

---

## 來自錄音室：為音序器編寫程式

製作人不會每天晚上都手動重播相同的鼓點節奏。他們只要將它編寫進音序器一次，需要時就讓它執行：

![Sequencer analogy for automations](assets/sequencer-automation.webp)

| 音序器 | 應用程式自動化 |
|---|---|
| 已編寫的節奏 | 已儲存的提示 |
| 按下播放 | 手動觸發 |
| 依時鐘執行 | 排程 |
| 選定的樂器 | 選取的工具 |
| 製作人檢查混音 | 人員檢閱結果 |

先從手動自動化開始，這樣你就可以在為它設定排程或觸發程序之前，安全地測試提示。

## 核心概念

### 自動化是已儲存的代理程式執行

自動化有四個適合初學者的部分：

| 部分 | 問題 |
|---|---|
| 觸發程序 | 是什麼啟動它？ |
| 提示 | 代理程式應該做什麼？ |
| 工具 | 所需的最小存取權限是什麼？ |
| 檢閱路徑 | 我在哪裡檢查結果？ |

![Automation trigger to agent run](assets/automation-trigger-to-run.webp)

### 從手動和本機開始

手動自動化只會在你啟動它時才執行。測試時請讓它保持在本機。手動、本機的自動化是最安全的第一步，因為你可以：

- 測試提示
- 檢查輸出
- 調整範圍
- 避免意外的執行
- 確認工具已降到最少

![Start manual, then expand automations](assets/manual-first-path.webp)

只有在手動提示產生值得信賴的結果之後，才加入排程、事件觸發程序或雲端執行。

### 良好的入門自動化

選擇你已經做過不只一次的工作：

- 「哪些提取要求被卡住，它們接下來需要什麼？」
- 「在開啟 PR 之前，我應該執行哪些驗證步驟？」

避免第一個自動化就撰寫程式碼、張貼註解、變更標籤、核准檢閱或合併提取要求。

> [!TIP]
> 自動化儲存在應用程式中，不會隨儲存庫一起提交。請將提示視為正式環境的指引：不要放入機密，並且只給自動化它所需要的工具。

> [!WARNING]
> 問題的標題和內文可能包含不受信任的文字。唯讀摘要比張貼註解或編輯儲存庫的自動化更安全。這可以降低**提示插入（prompt-injection）**的風險，也就是惡意文字試圖操控代理程式。

---

## 實作練習

在這些練習中，你將：

- 建立手動的檢閱就緒報告自動化
- 執行它並檢查其歷程記錄
- 排程該自動化，使其每天在沒有手動觸發的情況下執行

### 確認工作項目存在

在建立自動化之前，請確認工作項目存在。在側邊欄中選取 **Pull requests**，然後選取 **Authored by me**。將你的 fork 的 `repo:` 詞彙新增到該索引標籤的查詢中，如[第 03 章](../03-development-workflows/README.zh-Hant.md#4-find-work-in-issues-and-pull-requests)所示。

在 **Authored by me** 索引標籤上，你應該會看到至少三個來自第 00 章設定指令碼的開啟中提取要求。如果你完成了第 03 章，檢閱註解和失敗的檢查可能已經修復，而且你的搜尋修正提取要求也會出現。這是預期的；報告會顯示你的提取要求目前所處的任何狀態。如果清單是空的，請在繼續之前，[立即執行該指令碼](../00-setup/README.zh-Hant.md#seed-the-repository)。

### 練習：建立手動檢閱就緒報告

這個自動化回答一個真實的每日問題：哪些提取要求已準備好合併，而尚未就緒的又被什麼卡住？

**Pull requests** 檢視已經列出你開啟中的提取要求，但它不會綜合整理這些提取要求的合併就緒程度。檢閱就緒報告則加入該分析：CI 狀態、未解決的註解，以及每個 PR 接下來需要什麼。

若要建立自動化：

1. 在側邊欄中開啟 **Automations**。
1. 選取 **New automation**。
1. 為自動化提供名稱：`PR review readiness report`
1. 將 **Trigger** 從 **Daily** 變更為 **Manual**。
1. 確認 **Run in the cloud** 已關閉，讓自動化在本機執行。
1. 貼上下方的提示：

   ```text
   For each open pull request in this repository, report:
   - PR number, title, and branch
   - CI check status (passing, failing, or pending) and the name of any failing check
   - Whether there are unresolved review comments, and a one-line summary of each
   - Whether the branch is up to date with the default branch
   - The single next human action needed to move it toward merge

   Present the results as a table. Do not edit files, add comments, change labels, approve reviews, or merge anything.
   ```

1. 在提示下方，使用 **Select repository** 選擇你的 `copilot-app-for-beginners` 儲存庫。提示中說的是「this repository」，因此自動化必須以你的 fork 為目標。
1. 在儲存庫旁邊出現的 **Workspace** 選擇器中，選取 **Current checkout**。此報告只讀取資料，因此每次執行不需要自己的新 worktree。
1. 選取 **Create** 旁的箭頭，然後選取 **Create and run**。

   ![New automation form with the Manual trigger, Run in the cloud off, the course repository, Current checkout, and the Create and run option open](assets/app-new-automation.webp)

   **預期輸出：** 你的自動化應該會出現在 **Your automations** 底下，且觸發程序為 **Manual**。執行應該會立即開始，並出現在 **Recent runs** 底下。

1. 等待執行完成。狀態應該會變更為成功指示。

1. 開啟執行詳細資料並確認：
   - **提示**與你上方貼上的內容相符。
   - 沒有出現**錯誤文字**。如果結果是空的，請檢查第 00 章設定指令碼是否已建立練習用的提取要求。
   - **結果**包含開啟中提取要求的表格，其中有每個提取要求的 CI 狀態、註解狀態和下一步動作。來自種子 PR 的代表性結果如下所示：

   | PR | CI | 註解 | 下一步動作 |
   |---|---|---|---|
   | #6 Improve empty-state copy | ✅ Pass | ⚠️ 1 unresolved; reviewer asks for more helpful guidance | Address the review comment |
   | #7 Failing stats check practice | ❌ Fail: Book app web | None | Fix the favorite count in `ReadingStats.tsx` |
   | #8 Reading dashboard merge-readiness practice | ✅ Pass | None | Ready to merge |

你的 PR 編號、標題和狀態可能不同。重點是每一列都包含 CI 狀態、註解狀態和具體的下一步動作。如果表格是空的，請確認第 00 章設定指令碼已建立練習用的提取要求，然後檢查儲存庫權限。

**運作方式：** 自動化會儲存提示和觸發程序，讓你稍後可以再次執行相同的限定範圍任務。由於提示是唯讀的，因此在你學習檢閱迴圈時風險保持在低點。不同於只列出開啟項目的 **Pull requests** 檢視，這份報告會說明 PR 為什麼被卡住，而不只是它存在。

---

### 練習：排程檢閱就緒報告

手動報告運作正常。現在讓它每天早上執行，這樣當你開始一天的工作時，摘要就已經在等著你。

> [!NOTE]
> 此自動化是本機的。排程的本機執行只有在你的電腦處於喚醒狀態且 GitHub Copilot 應用程式正在執行時才會發生。如果你的電腦在排程時間處於睡眠狀態，則不會執行。

1. 在側邊欄中開啟 **Automations**，並找到你的 `PR review readiness report`。
1. 選取該自動化，然後選取 **Edit**。
1. 將 **Trigger** 從 **Manual** 變更為 **Daily**。
1. 在 **Hours** 中，選取你工作日開始之前的時間，例如 **08:00**。此欄位允許選取多個小時。請清除任何其他已選取的小時，包括已選取的預設 **09:00**，讓只剩下你選擇的小時處於選取狀態。將 **Minute** 保持在 **:00**。

   儲存之前，請縮小範圍，讓排程的報告保持實用：

      - 確認儲存庫選擇器仍然顯示你的 `copilot-app-for-beginners` 儲存庫，讓自動化只以你的 fork 為目標。
      - 如果提示在執行幾次後輸出太雜亂，請新增標籤或分支篩選來減少輸出。

1. 選取 **Save automation**。

   儲存之後：

   - 檢查自動化卡片。它應該顯示 **Daily** 觸發程序。
   - 開啟自動化並選取頂端的名稱。**Automation details** 會顯示 **Schedule** 和 **Next run** 時間。
   - 隔天早上，檢查 **Recent runs** 並確認有新的執行完成。
   - 如果摘要太長或太雜亂，請收緊提示。例如，將它限制為只包含檢查失敗的 PR，然後再次儲存。

   ![Automation details for the PR review readiness report showing Daily at 8:00 AM and its Next run time](assets/app-automation-schedule.webp)

   此圖片顯示首次執行前已儲存的排程。請使用 **Recent runs** 來檢查你自己的自動化的結果。

**預期輸出：** 自動化卡片顯示 **Daily** 觸發程序，且 **Automation details** 顯示下一次執行的時間。如果應用程式在排程時間正在執行，新的執行會出現在 **Recent runs** 底下，而不需要你選取 **Run**。如果結果很雜亂，你就知道要在哪裡縮小提示的範圍。

### 為什麼要排程而不是手動執行？

手動自動化回答「現在的狀態是什麼？」排程自動化則回答「自昨天以來有什麼變更？」，而不需要你記得去詢問。

> [!TIP]
> 應用程式也支援 **Hourly**、**Weekly** 和 **CRON** 排程。CRON 提供最多的控制。應用程式會驗證運算式，並在你儲存之前顯示人類可讀的預覽。

---

<details>
<summary>選用：事件觸發程序和雲端自動化</summary>

## 事件觸發程序

排程依時鐘執行。事件觸發程序則是回應儲存庫中發生的事情而執行。

目前的 Trigger 功能表包含這些事件觸發程序：

| 觸發程序 | 觸發時機... | 使用範例 |
|---|---|---|
| **Issue** | 發生相符的問題事件 | 摘要問題活動以便分流 |
| **Automation failed** | 上游自動化失敗 | 回報失敗的自動化任務 |
| **Automation completed** | 你在 **Upstream Automations** 底下指定的自動化完成 | 在分流執行之後啟動後續報告 |
| **Code scanning alert** | 發生相符的程式碼掃描警示事件 | 摘要警示以供人員檢閱 |
| **Dependabot alert** | 發生相符的 Dependabot 警示事件 | 摘要相依性警示 |
| **Discussion comment** | 在討論中新增註解 | 摘要新的討論意見回饋 |
| **Discussion opened** | 開啟討論 | 準備簡短的討論摘要 |
| **Discussion updated** | 更新討論 | 回報討論中有什麼變更 |
| **Pull request** | 發生相符的提取要求事件 | 為檢閱者摘要變更 |
| **Secret scanning alert** | 發生相符的祕密掃描警示事件 | 摘要警示以供人員檢閱 |
| **Sub issue added** | 新增子問題 | 回報問題拆解的變更 |
| **Workflow** | 你指定的 GitHub Actions 工作流程完成（例如失敗） | 摘要 CI 執行失敗的原因 |

選擇符合你希望自動化處理的事件的觸發程序。**Hourly**、**Daily**、**Weekly** 和 **CRON** 等排程選項會出現在同一個功能表中。此功能表比其視窗還長，因此請捲動它來查看每個選項。第一張螢幕擷取畫面顯示功能表的頂端，第二張則顯示結尾。

<img src="assets/app-automation-new-triggers.webp" alt="新增自動化表單中的 Trigger 功能表，顯示 Manual、Hourly、Daily（已選取）、Weekly、CRON、Issue、Automation failed、Automation completed、Code scanning alert、Dependabot alert 和 Discussion comment" width="800" />

<img src="assets/app-automation-new-triggers-scrolled.webp" alt="捲動到結尾的 Trigger 功能表，顯示從 Automation failed 到 Workflow，包括 Pull request、Secret scanning alert 和 Sub issue added" width="800" />

### 確保事件觸發程序安全

由事件觸發的自動化需要格外小心，因為它們會對外部輸入做出反應：

1. **從唯讀開始。**摘要比張貼註解或編輯儲存庫的自動化更安全。
2. **限制儲存庫範圍。**以單一儲存庫為目標，而不是你可存取的每個儲存庫。
3. **依標籤或搜尋查詢篩選。**縮小問題或 PR 觸發程序的範圍，讓自動化不會在每個新項目上觸發。
4. **在摘要可靠之前避免寫入工具。**廣泛的觸發程序搭配寫入工具會增加提示插入的風險。在啟用會推送變更或新增註解的工具之前，請先檢閱數次執行。
5. **定期檢閱執行歷程記錄。**檢查自動化是否以預期的頻率觸發並產生有用的輸出。

## 雲端自動化

到目前為止你建立的每個自動化都是**本機**的：它在應用程式開啟時從你的電腦執行。**雲端自動化**則在 GitHub 代管的基礎結構上執行，因此即使你的筆記型電腦已關閉，它也可以觸發。

![Local versus cloud automations](assets/local-vs-cloud-automations.webp)

### 何時考慮雲端

只有在本機版本可靠運作且你了解權限模型之後，才將自動化移到雲端。雲端適合用於下列情況：

- 自動化需要在夜間或週末執行。
- 多位團隊成員應該看到相同的執行歷程記錄。
- 觸發程序是可能隨時發生的事件（建立問題、開啟 PR）。

雲端自動化有一些你可能無法控制的需求：

- 儲存庫必須啟用 **Copilot cloud agent**，且組織必須允許自動化。
- **儲存庫可見度**：儲存庫必須是私人或內部的。雲端自動化無法在公開儲存庫中使用。此公開課程儲存庫的 fork 是公開的，因此請閱讀本節，但不要建立雲端自動化。
- **計費**：每次雲端執行都會啟動 Copilot cloud agent 工作階段，使用 GitHub Actions 分鐘數和 AI 點數。**Personal** 自動化會向建立者收取 AI 點數；**Shared** 自動化則向儲存庫擁有者收取 AI 點數。在啟用高頻率排程之前，請檢查適用的 Actions 計費和 AI 點數限制。

當你啟用 **Run in the cloud** 時，會出現 **Tools** 下拉式清單。在繼續之前請檢查其初始選取項目；目前的表單在你選擇任何項目之前會顯示 **Select tools**。每個選取的工具都會授予雲端代理程式特定的能力，例如推送變更、更新標籤或建立提取要求。內建工具一律可用。如果你不確定要挑選哪些工具，請先撰寫你的指引，然後選取 **Suggest tools**。應用程式會根據你的指引新增工具，並保留你已選取的工具。儲存之前請先檢閱清單。

<img src="assets/app-automation-cloud-tools.webp" alt="雲端自動化表單，Tools 清單開啟在 Tools 按鈕上方。在 Pull Requests 群組中，Read PR、List PRs 和 Search PRs 已勾選，按鈕顯示 3 tools selected" width="800" />

此清單依領域將工具分組，例如 **Issues**、**Pull Requests**、**Discussions**、**Repos** 和 **Actions**。只選取任務所需的工具。使用 **Search tools…** 來尋找並選取每個必要的工具。如果你的應用程式一開始就已選取工具，請清除你不需要的工具。螢幕擷取畫面顯示在 **Pull Requests** 群組中選取了三個唯讀的提取要求工具（**Read PR**、**List PRs** 和 **Search PRs**）。同一群組也列出寫入工具，例如 **Create PR** 和 **Merge PR**，它們保持未勾選。這三個工具只是起點，並不證明報告具有所需的全部存取權限。如果缺少 CI 狀態，請檢查工具描述，並只新增擷取工作流程執行或檢查結果所需的唯讀工具。只列出工作流程定義無法顯示提取要求檢查的狀態。

在工具下方，**Type** 設定決定誰可以看到自動化以及誰支付其 AI 點數。**Personal** 是預設值：只有你可以看到自動化，AI 點數向你的使用者收費，且在 GitHub 上的動作使用你的身分識別。**Shared** 會讓有權存取儲存庫的每個人都能看到自動化，向儲存庫擁有者收取 AI 點數，並在 GitHub 上的動作使用 Copilot 的身分識別。請從 **Personal** 開始。

**Personal** 設定不會讓產生的雲端代理程式工作階段變成私人的。有儲存庫存取權的人可以看到這些工作階段及其記錄。對於兩種類型，都請不要在提示中放入機密。

</details>

---

## 疑難排解

如果某些功能未如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出了所有章節的問題和解決方法。

<details>
<summary>自動化問題</summary>

### 本機自動化沒有執行

本機執行只有在你的電腦處於喚醒狀態且 GitHub Copilot 應用程式正在執行時才會發生。另外請確認專案仍然連線，且提示所需的本機工具和認證仍然有效。

### 檢閱就緒報告是空的

確認設定指令碼已完成，且你的 fork 有開啟中的提取要求。請參閱[確認工作項目存在](#confirm-work-items-exist)。然後檢查你的儲存庫權限和提示中的任何篩選。

### 無法使用雲端自動化

雲端自動化無法在公開儲存庫中使用，而本課程的 fork 是公開的。在私人或內部儲存庫中，請檢查組織原則、儲存庫設定、計費，以及你選取的工具。請參閱[雲端自動化](#cloud-automations)。

### 排程的執行很雜亂

縮小提示的範圍、降低排程的執行頻率，或新增標籤或分支篩選來減少輸出。

### 自動化提出了令人意外的建議

讓提示更具體，並列出自動化不可以做的事。對於雲端自動化，也請清除任務不需要的工具。

</details>

---

## 重點整理

1. 每個自動化都需要觸發程序、提示、工具集和檢閱路徑。
2. 手動自動化是最安全的第一步。在加入排程之前先測試提示。
3. 唯讀的檢閱就緒報告是很好的第一個自動化，因為它提供了單靠 **Pull requests** 檢視所無法提供的分析。
4. 排程自動化回答「自昨天以來有什麼變更？」，而不需要你記得去詢問。
5. 事件觸發程序和雲端自動化留待之後。從唯讀開始、縮小篩選範圍，並檢查原則、計費和權限。
6. 套用最低權限原則：只給自動化它所需的工具，讓不受信任的問題或 PR 文字較難操控代理程式。

---

## 作業

![Assignment](../assets/assignment.webp)

為你自己的工作流程建立一個手動自動化：

1. 為它清楚命名。
2. 使用手動觸發程序。
3. 撰寫包含一項限定範圍任務的提示。
4. 保持唯讀。在提示中，告訴它不要編輯檔案、張貼註解或變更 GitHub 項目。本機自動化沒有工具清單。如果你之後將自動化移到雲端，請在其 **Tools** 清單中只選取唯讀工具。
5. 執行一次。
6. 檢查執行歷程記錄並修訂提示。

**成功準則：** 你能夠說明為什麼這個自動化可以安全地再次執行。

---

## 課程完成

該自動化是你的最後一個練習。以下回顧你在第 00 章到第 07 章中練習過的所有內容。

你已經從設定與入門，歷經工作階段、worktree 和脈絡；開發與 GitHub 工作流程迴圈；技能和自訂代理程式；MCP 伺服器和外掛程式；canvas；以及自動化。一路走來，有一個習慣始終不變：讓人員掌控品質與交付。

| 領域 | 你練習了什麼 |
|---|---|
| 工作階段與 worktree | 在限定範圍的分支和隔離的 worktree 中工作 |
| 脈絡 | 有意識地使用提示、檔案、問題和指引 |
| 開發與 GitHub | 以測試、建置、瀏覽器預覽和差異來規劃、變更和驗證，然後透過問題、PR、檢閱註解和失敗的檢查推進工作 |
| 指引與角色 | 儲存庫指引、可重複使用的技能，以及唯讀的自訂代理程式 |
| 連接的工具與套件 | 用於文件的 MCP 伺服器，以及提供功能的外掛程式 |
| 可見度 | 用於共用、可檢查狀態的 canvas |
| 重複 | 安全使用的手動與排程自動化 |

最後那個習慣就是重點所在：在每個主要控制點，人的判斷都留在迴圈中，包括實作開始之前、開啟提取要求之前，以及啟用任何合併自動化之前。先從小型的真實問題開始練習，然後在工作保持獨立、經過驗證且可供檢閱時，再加入進階的工作流程。

**[← 返回第 06 章](../06-canvases/README.zh-Hant.md)** | **[返回課程首頁 →](../README.zh-Hant.md)**

---

## 來源參考

- [Using automations in the GitHub Copilot app](https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations)
- [Copilot automations](https://docs.github.com/en/copilot/concepts/agent-capabilities/agent-powered-workflows-and-operations/about-automations)
- [GitHub Copilot app generally available](https://github.blog/changelog/2026-06-17-github-copilot-app-generally-available/)
- [GitHub Copilot app product blog](https://github.blog/news-insights/product-news/github-copilot-app-the-agent-native-desktop-experience/)
