![第 01 章：導覽應用程式](assets/chapter-header.svg)

> **如果您能從第一個提示一路進行到提取要求，而不必在多個工具之間手動拼湊脈絡，會是什麼樣子？**

現在應用程式已安裝並連結到課程存放庫，本章將回答一個問題：如果您已在編輯器或終端機中使用 GitHub Copilot，桌面應用程式為什麼仍然有幫助。接著您將導覽主要的導覽列，並比較聊天與專案工作階段。您也會探索用於長期目標的 Projects 功能。您將學到工作階段模式如何控制代理程式自行處理的程度、如何選擇模型與推理強度，以及在哪裡可以找到應用程式設定。

## 學習目標

完成本章後，您將能夠：

- 說明為什麼相較於在編輯器中使用 Copilot 或在終端機中使用 Copilot CLI，您應該使用 GitHub Copilot app
- 識別應用程式的主要功能與設定
- 針對任務在聊天與專案工作階段之間做出選擇
- 說明如何使用 Projects 來協調朝向長期目標的工作
- 說明工作階段模式：Interactive、Plan 與 Autopilot
- 根據任務的複雜度選擇模型與推理強度
- 使用提示歷程記錄來重覆使用並搜尋先前的提示
- 以不同的模型或推理強度重試回應
- 試用語音聽寫（選用）

> ⏱️ **預估時間**：約 30 分鐘

## 先決條件

如果您直接跳到這裡，請先暫停並完成[第 00 章：設定](../00-setup/README.zh-Hant.md)，以安裝應用程式並連結您的存放庫。

## 為什麼要使用 GitHub Copilot app？

如果您已經在 VS Code 中使用 GitHub Copilot，或在終端機中使用 Copilot CLI，為什麼還要費心使用另一個應用程式？

編輯器或終端機中的 GitHub Copilot 非常適合處理您已開啟的程式碼。比較困難的部分是從頭到尾監督代理程式的工作：規劃、隔離的編輯、測試、預覽、議題與提取要求。當這些工作分散在各個工具中，您必須自己拼湊來龍去脈，並自問「我剛才做到哪裡了？」

這個應用程式不會取代您的編輯器。它提供單一桌面控制中心，讓您執行並檢視代理程式的工作：

| 挑戰 | 感受 | Copilot app 新增的功能 |
|---|---|---|
| 共用的工作副本 | 兩個代理程式任務動到同一個資料夾與分支，變更混在一起 | 專案工作階段讓專注的工作彼此分開（第 02 章說明 worktree） |
| 分散的佐證資料 | 在聊天中規劃、在編輯器中看 diff、在終端機中跑測試、在瀏覽器中看 PR | 工作階段、diff、終端機輸出、瀏覽器預覽與 GitHub 都在同一個應用程式中 |
| 探索與變更 | 一個快速的提問與真正的程式碼變更感覺相同，直到檔案開始變動 | 聊天用於安全的提問；準備好在存放庫中工作時使用專案工作階段 |
| 進度被埋在聊天中 | 計畫步驟、檢查與筆記在長長的聊天討論串中捲走，所以您一直詢問代理程式目前的進度 | Canvas 在側邊面板中為您與代理程式提供共用的看板，雙方都可以更新（第 06 章說明 canvas） |
| 重複的工作 | 您每週都重新輸入相同的提示，用於 PR 摘要、檢查或清理 | Automation 會儲存提示，並可依需求、依排程或由 GitHub 事件執行 |

![編輯器與 GitHub Copilot app 的比較](assets/editor-vs-app.webp)

請保留 VS Code、Visual Studio、JetBrains 或您慣用的編輯器來進行深入編輯。在應用程式中，只要您想要完整的編輯器，隨時都可以在 VS Code 中開啟同一個專案。

## 導覽應用程式

開啟 GitHub Copilot app，並留意側邊欄中的這些區域：

### New

這是起始檢視。您可以在沒有專案的情況下開始聊天、選取已連結的專案以進行專案工作階段、選擇模式與模型，或從建議的提示之一開始。

![New 檢視](assets/app-new-page.webp)

### Pull requests

此檢視列出與您相關的 GitHub 提取要求。**Authored by me** 與 **Review requests** 等索引標籤可篩選清單。使用篩選圖示與 `repo:OWNER/REPOSITORY` 查詢，可將結果限制在單一存放庫。第 03 章會示範步驟。開啟提取要求可閱讀其檢查與留言，或啟動工作階段來處理它。在設定章節中建立的練習提取要求會出現在 **Authored by me** 索引標籤。

![Pull requests 檢視](assets/app-pull-requests-page.webp)

### Issues

此檢視對 GitHub 議題的運作方式相同，具有 **Assigned to me** 與 **Created by me** 等索引標籤。您可以從議題啟動工作階段，或選取 **New issue** 來建立議題。在設定時建立的練習議題已指派給您，因此會出現在 **Assigned to me** 索引標籤。

![Issues 檢視](assets/app-issues-page.webp)

### Dashboards

此檢視將您的 GitHub 工作顯示為小工具的看板，每個小工具是顯示篩選後清單的小型面板。您無需開啟每份清單，就能看到哪些事項需要您注意。預設的 **My Work** 儀表板包含這些小工具：**Pulls created**、**Pulls mentioned**、**Issues assigned**、**Issues mentioned** 與 **Requested reviews**。在設定章節中建立的練習提取要求與議題會出現在 **Pulls created** 與 **Issues assigned** 中。

![Dashboards 檢視，顯示 My Work 儀表板及其 Pulls created、Issues assigned、Requested reviews、Pulls mentioned 與 Issues mentioned 小工具](assets/app-dashboards-page.webp)

### Automations

**Automations** 檢視是您建立週期性代理程式任務的地方。此檢視顯示可手動執行，或以每日、每週頻率執行的範本。Automation 也可以由事件觸發。您在設定 automation 時選擇在本機或雲端執行。第 07 章說明 automation。

![Automations 檢視](assets/app-automations-page.webp)

### Customize

在這裡您可以管理擴充應用程式的各種方式：技能、Model Context Protocol (MCP) 伺服器、外掛程式、擴充功能與 canvas。您現在還不需要用到其中任何一項。第 04 章說明技能與自訂代理程式，第 05 章說明 MCP 伺服器與外掛程式，第 06 章則介紹 canvas。

![Customize 檢視，已選取 Browse 索引標籤，顯示搜尋方塊、Editor's picks 與 MCP 伺服器清單](assets/app-customize-page.webp)

### Sessions and Chats

側邊欄的 **Sessions** 區域列出每個已連結的專案。在您開始第一個聊天之後，那裡也會出現 **Chats** 列。[聊天、專案工作階段與模式](#chats-project-sessions-and-modes)一節說明何時使用每種工作階段類型。

![側邊欄的 Sessions 區域，顯示含一個聊天的 Chats 列，以及含一個工作階段的 copilot-app-for-beginners 專案](assets/app-sessions-and-chats.webp)

### Projects

> [!NOTE]
> Projects 處於**公開預覽版**，可能會有所變動。它對本課程是選用的。若要開啟它，請開啟 **Settings** > **Experimental**，然後開啟 **Projects**。

**Project** 為 Copilot 提供一個長期目標。您描述目標，Copilot 負責協調工作。當您提出要求時，它可以啟動工作階段、建立報告等成品，並設定週期性任務。Project 可以使用一個存放庫、多個存放庫，或不使用任何存放庫。您可以在同一個地方追蹤它的所有工作，而不必在每個工作階段中分別查看。

例如，Project 可以收集關於課程 Book App 的意見回饋、找出常見問題，並啟動後續工作。當您收到新的意見回饋時，就回到同一個 Project，而不必重新開始。

Project 與**專案工作階段**不同，後者是單一存放庫中的一個工作階段。

![Projects 檢視，顯示側邊欄中的 Book App Project、其對話，以及 Overview 面板，其中 Done 下有一個已完成的工作階段](assets/app-projects-page.webp)

<details>
<summary>了解如何啟用與使用 Projects</summary>

本課程使用**專案工作階段**來指連結到存放庫的工作階段。**Projects** 是另一項功能，用於跨任務與工作階段協調工作。將存放庫新增到應用程式並不會建立這種 Project。

| 使用這個 | 當您需要... |
|---|---|
| 聊天 | 在不選取存放庫的情況下提出一般問題 |
| 專案工作階段 | 在特定存放庫中檢查、規劃或變更程式碼 |
| Project | 協調朝向長期目標的相關工作 |

#### 啟用 Projects

使用側邊欄底部的齒輪圖示開啟 **Settings**。選取 **Experimental**，然後在 **Orchestration** 下開啟 **Projects**。若要快速找到該開關，請在 **Filter experiments** 方塊中輸入 `Projects`。

![已選取 Experimental 的 Settings，實驗項目已篩選為 Projects，且 Orchestration 下的 Projects 開關已開啟](assets/app-enable-projects.webp)

關閉 **Settings**。**Projects** 區域會出現在側邊欄中。如果您的應用程式中沒有這個開關，請繼續進行導覽的其餘部分。

#### 啟動 Project

選取 **Projects** 旁邊的 **+**（標註 1）以開啟 **New project**。頁面會顯示起點，例如 **Start with one or more repositories**、**Work on parallel tasks** 與 **Triage recent issues**。

存放庫是選用的。若要新增存放庫，請選取 **Start with one or more repositories**（標註 2）。在 **Which repositories should this project use?** 下，選取建議項目，或依存放庫擁有者與名稱搜尋，然後選取該存放庫（標註 3）。選取 **Continue**（標註 4）。Copilot 會為 Project 命名，並詢問您想處理什麼。

![New project 頁面，以標註 1 到 4 標示 Projects 旁的加號、Start with one or more repositories、已選取的訓練存放庫與 Continue](assets/app-new-project.webp)

在提示方塊中，描述目標、第一個任務、預期結果與任何限制。例如：

```text
Use this project to track improvements to the book app in samples/book-app-web.
First, explain the current search, filters, and reading statistics.
List three questions we can use to collect learner feedback.
Do not change files, open pull requests, or create scheduled tasks.
```

送出提示後就會開始工作。您不需要啟動 Project 也能完成這次導覽。

#### 追蹤進度並共用脈絡

右側面板具有對應 Project 工作的索引標籤：

| 索引標籤 | 您可以做什麼 |
|---|---|
| **Overview** | 檢視 **Needs input**、**Open**、**Working** 與 **Done** 下的工作。**Open** 包含具有開啟中提取要求的工作。選取工作階段可檢查其對話、變更、提取要求或計畫。使用 **Steer this session** 為該工作階段提供進一步的指示。 |
| **Library** | 新增輔助檔案與資料夾，並開啟產生的成品。在 **All Files** 下，**Agent** 包含產生的成品，**User** 包含您新增的資料。 |
| **Automations** | 檢查週期性任務、其排程與下次執行時間。您可以從其功能表編輯排程、暫停任務或刪除它。 |

開啟 **Overview** 以找出需要您注意的工作。檢閱 **Needs input** 以處理決策、檢視與權限要求。在本節開頭的螢幕擷取畫面中，Project 啟動了一個唯讀工作階段，該工作階段完成後會顯示在 **Done** 下。

開啟 **Library** 以共用輔助資料。選取 **+**（**Add to Library**）（標註 1），然後選取 **Add file…** 或 **Add folder…**（標註 2）。告訴 Copilot 該任務要使用哪些資料。

![Library 索引標籤，已開啟 Add to Library 功能表，顯示 Add file 與 Add folder，並以標註 1 和 2 標示](assets/app-project-library.webp)

若要要求週期性工作，請在 Project 的對話中描述任務與排程。例如，您可以要求每週在其 Library 中提供意見回饋摘要。開啟 **Automations** 以檢查產生的排程。在您要求週期性工作之前，該索引標籤是空的。第 07 章會更詳細說明 automation。

![Project 的 Automations 索引標籤，尚無週期性任務](assets/app-project-automations.webp)

回到 Project 的對話，以提供後續任務或變更目標。如需更多範例，請參閱[在 GitHub Copilot app 中使用 Projects][projects]。

</details>

## 聊天、專案工作階段與模式

### 來自錄音室：在控制室中

控制室中的製作人不會以相同的方式處理每一首歌。有些快速的問題只需要快速的答案。有些樂器的錄製需要密切的指導。有些則需要先把編曲整理成譜。

![製作人的控制室，具有 Chat Session 與 Project Session 切換開關，以及混音台上的 Interactive、Plan 與 Autopilot 區段](assets/producer-control-room-modes.webp)

GitHub Copilot app 的運作方式也是如此。首先您選擇工作階段的類型。接著您選擇一種模式，決定您指導工作的緊密程度。

| 使用這個 | 當您想要... | 是否建立分支或 worktree？ |
|---|---|---|
| 聊天工作階段 | 提問、腦力激盪、摘要、熟悉環境 | 否 |
| 專案工作階段 | 規劃、檢查、編輯、測試，或建立可供 PR 的工作 | 通常會，取決於工作階段設定 |

### 聊天工作階段

> 聊天就像是向音樂家問一個快速的問題。

聊天工作階段可協助您學習與腦力激盪，而不必啟動分支。它們適用於在選擇存放庫之前提出一般問題。試試看：

- 選取 **Sessions** 旁邊的 **+**（標註 1），然後選取 **Chat**（標註 2）。
   ![Sessions 功能表，標註了 + 與 Chat](assets/app-quick-chat.webp)
- 確認提示方塊下方的專案選擇器顯示 **Chat**。
- 送出下面的提示：

   ```text
   In the GitHub Copilot app, explain when I should use a chat and when I should use a project session. Do not create or change files.
   ```

**預期輸出：** 應用程式應該說明聊天適用於一般問題，而專案工作階段為程式碼工作提供存放庫脈絡。它不應該變更任何檔案，而且應用程式不會為聊天建立分支或 worktree。

### 專案工作階段

當代理程式需要存放庫脈絡、需要變更程式碼，或必須建立提取要求等成品時，請使用專案工作階段。大多數專案工作階段應該使用 **New worktree**。獨立的 worktree 讓代理程式可以同時工作，而不會變更同一個工作副本。第 00 章的設定工作階段是刻意的例外，因為您為一次性的設定選取了 **Current checkout**。應用程式會為您下一次以 **+** 啟動的工作階段記住該選擇，因此您會在第 02 章切換回 **New worktree**。

回到您先前建立的專案工作階段。它列在側邊欄中的 `copilot-app-for-beginners` 底下。送出這個提示：

```text
I'm learning the copilot-app-for-beginners course. Give me a beginner-friendly tour of the samples/book-app-web sample before I begin the hands-on exercises. Explain what the app does, the technologies it uses, how its main files are organized, how data flows through it and what the tests cover. Refer to specific file paths and finish with a suggested reading order. Do not change any files.
```

**預期輸出：** 應用程式應該描述範例的用途、技術、主要檔案、資料流程與測試。它應該參考 `samples/book-app-web` 底下的路徑，並保持檔案不變。

> [!NOTE]
> 本課程其餘部分的實作練習都專注於 `samples/book-app-web`。這次導覽讓您在開始使用該範例之前，先對它建立一個心智模型。

### 模式

> - **Interactive 模式**就像是在錄製樂器時頻繁確認、持續互動地進行指導。
> - **Plan 模式**就像是在第一次錄製之前，先整理並核准編曲。
> - **Autopilot** 就像是將定義明確的任務交給值得信賴的系統，讓它在極少介入的情況下完成。

![工作階段模式：Interactive、Plan 與 Autopilot，自主程度由左至右遞增。選擇適合任務的模式。](assets/session-mode-decision-ladder.webp)

| 模式 | 應用程式如何詮釋 | 使用案例 |
|---|---|---|
| Interactive | 「逐步協作」 <br> Copilot 與您逐步一起工作 | 您希望參與整個流程 |
| Plan | 「先規劃，準備好再執行」 <br> Copilot 在執行之前先建立計畫 | 初始方法與專案細節很重要 |
| Autopilot | 「不中斷的端對端執行」 <br> Copilot 獨立工作 | 定義明確且結果清楚的任務 |

模式功能表也有 **Tool permissions**。此設定控制代理程式在使用工具（例如終端機命令）之前是否詢問您。**Always ask** 每次都詢問。**Approve all** 不詢問就執行工具。**Assisted** 只在工具要求未通過 AI 安全檢查時才詢問。請檢查此設定，以便知道何時會出現核准要求。

## 模型與推理強度

工作階段模式控制代理程式獨立工作的程度。模型與推理設定則控制它如何處理您的要求。

| 控制項 | 它會變更什麼 |
|---|---|
| **Model** | 處理您要求的 AI 模型。各模型在功能、速度與使用成本上有所不同。 |
| **Reasoning effort** | 支援的模型在處理任務時使用多少推理。較高的強度有助於處理複雜的問題，但可能需要更長的時間。 |
| **Context window** | 模型一次可以容納多少文字，包括對話與它讀取的檔案。較大的視窗有助於長時間的工作階段或大型程式碼庫，但可能使用更多 AI 點數。 |

模型控制項位於提示方塊下方，緊鄰模式選擇器。它會顯示 **Copilot Fusion** 或模型名稱。可用的控制項取決於您的選擇與應用程式版本。選擇個別模型時，您可以找到 **Model**、**Effort** 與 **Context window**。選擇 **Copilot Fusion** 時，功能表則改為顯示 **Effort level**。

對於存放庫導覽或簡短的說明，請使用 **Standard** 的 **Copilot Fusion**，或保留您目前的模型及其預設推理強度。對於困難的錯誤或跨多個檔案的變更，請考慮較高的 Fusion 強度等級，或適合複雜程式設計的模型。較高的強度並不保證答案正確。

本課程不需要特定的模型。可用的模型、強度等級與 context window 大小各不相同。有些模型可能不提供強度或 context window 設定。

### Copilot Fusion

**Copilot Fusion** 出現在模型控制項中，但它不是單一模型。它會為每個要求選擇模型與工作流程，然後傳回單一回應。它可以使用一個模型、請較強的模型改進草稿，或請另一個模型檢視結果。

Copilot Fusion 與 **Autopilot** 不同。Fusion 控制模型如何處理您的要求。Autopilot 控制代理程式獨立工作的程度。

開啟模型控制項即可看到 **Copilot Fusion** 開關及其強度等級。如果您想選擇個別模型，請關閉該開關。下圖顯示已選取 **Standard**。

![Copilot Fusion 功能表，已選取 Standard，並提供 High 與 Max 強度等級](assets/app-copilot-fusion-options.webp)

| 強度等級 | 何時使用 |
|---|---|
| **Standard** | 日常程式設計任務。優先使用多功能的模型以提升效率。本課程請從這裡開始。 |
| **High** | 需要更強大的模型與更深入推理的挑戰性任務。 |
| **Max** | 需要最強大的模型與複雜推理的最嚴苛任務。 |

這些等級引導 Copilot Fusion 的模型選擇。它們不是單一固定模型的推理設定。較高的等級可能需要更長的時間並使用更多 AI 點數。它們不保證結果正確，因此請持續檢視變更並執行測試。

可用性取決於您的應用程式版本、方案與組織原則。您可以使用 **Copilot Fusion** 或其他可用的模型完成本課程。

功能表的 **Learn more** 連結會開啟 [Copilot Fusion 文件][copilot-fusion]。該頁面可能顯示較舊的功能表標籤。請依照此處所示的目前應用程式控制項操作。

### 試用控制項

1. 保持專案工作階段開啟。在提示方塊下方選取 **Interactive** 模式。
2. 開啟模型控制項。如果已選取 **Copilot Fusion**，請保留 **Standard**。否則，請使用 **Model** 選取可用的模型。
3. 如果有 **Effort** 或獨立的推理控制項，請開啟它並檢視各等級。對於這個簡單的問題，請保留預設值。
4. 送出這個提示：

    ```text
    In samples/book-app-web, explain how filterBooks searches book titles and authors. Does letter case affect the results? Refer to the relevant file. Do not change any files.
    ```

**預期輸出：** Copilot app 應該說明搜尋比對書名與作者時不受字母大小寫影響。它應該參考 `samples/book-app-web/src/App.tsx`，並保持檔案不變。

您可以在工作階段期間變更模型與推理強度，而不必變更其模式。

### 以不同的模型再試一次

如果代理程式的回應不符合您的需求，您可以用不同的模型或推理強度重試，而不必重新輸入提示。請在對話中尋找回應下方出現的 **Retry** 按鈕。選取它即可使用不同的模型、強度等級或 context window 重試。當第一次嘗試不太符合您的需求，或較高的推理強度可能有助於處理棘手的問題時，這會很有用。

## 提示與快速鍵

您大部分的時間會花在工作階段下方的撰寫方塊中輸入提示。一些實用的快速鍵與功能可讓撰寫提示更輕鬆：

- **提示歷程記錄**：按 **Ctrl+R**（在 macOS 上為 **Cmd+R**）以查看您在所有工作階段中最近的提示。您可以搜尋這些提示，並選取其中一個來重覆使用。當您定期執行類似的工作時，這可以節省時間。
- **附加脈絡**：輸入 `@` 以附加檔案與資料夾，輸入 `#` 以附加議題與提取要求，輸入 `/` 以執行命令。第 02 章會詳細說明脈絡。

## 設定

選取側邊欄底部的齒輪圖示以開啟 **Settings**。您現在不需要變更任何內容，但了解每個選項位於何處會有幫助。

![Settings 對話方塊，以箭頭指向側邊欄底部的齒輪圖示](assets/app-settings.webp)

<details>
<summary>設定參考：每個區域控制什麼</summary>

| 設定 | 您可以做什麼 |
|---|---|
| General | - 檢查應用程式更新 <br> - 檢視或變更存放庫的儲存位置 <br> - 變更佈景主題與其他應用程式偏好設定 |
| Accounts | - 檢視個人與企業帳戶資訊 <br> - 新增另一個 GitHub 帳戶 |
| Sessions | - 管理工作階段與聊天 <br> - 設定回應風格 <br> - 設定應用程式層級的指引 <br> - 設定分支命名與工作階段行為 |
| Themes | 選取並自訂應用程式佈景主題 |
| Accessibility | - 顯示縮放、鍵盤快速鍵 <br> - 通知與公告 |
| Voice dictation | - 麥克風設定 <br> - 啟用的鍵盤快速鍵設定 <br> - 轉譯模型 |
| Customize | 帶您前往側邊欄中的 **Customize** 檢視，您可以在那裡管理 MCP 伺服器、外掛程式、技能與 canvas |
| Model providers | 使用您自己的 API 金鑰設定來自其他提供者的自訂模型 |
| Experimental | 試用可能變更或移除的預覽功能 |
| Repositories | 每個存放庫的設定，例如 **Show in sidebar**、沙箱規則與預設分支 |

**Model providers** 設定會將應用程式連線到其他提供者。它不會取代提示方塊下方的模型選擇器。如果已有可用的模型，您不需要為這個練習設定提供者。

</details>

<details>
<summary>選用：語音聽寫</summary>

語音聽寫會將語音轉成可編輯的提示文字，在建立提示時可節省時間與精力。它預設為啟用，但您可以自訂麥克風設定與轉譯模型。

回到 GitHub Copilot app 的 **Settings** 對話方塊。選取 **Voice dictation** 以設定您的輸入裝置，並在需要時自訂設定。

> **Note:** 麥克風權限是在作業系統層級授與的。請依照作業系統的提示，允許 GitHub Copilot app 使用麥克風。

1. 在 **Microphone privacy** 下，選取 **Open preferences**，並確保 GitHub Copilot app 具有使用麥克風所需的權限。
2. 選取 **Test mic** 以驗證它是否正常運作。
3. 記下啟用語音聽寫的鍵盤快速鍵。
4. 結束 **Settings** 對話方塊並返回主應用程式視窗。
5. 開始新的聊天（選取 **Sessions** 旁邊的 **+**，然後選取 **Chat**），並使用鍵盤快速鍵測試語音聽寫。

</details>

---

## 疑難排解

如果某些功能沒有如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出所有章節的問題與解決方法。

<details>
<summary>初次導覽的問題</summary>

### 我找不到章節中顯示的設定

設定可能因應用程式版本、作業系統、組織原則與已啟用的功能而有所不同。請尋找最接近的類別，如果畫面仍然不符，請查閱官方文件。

### 語音聽寫無法運作

請檢查麥克風權限、本機轉譯模型的下載狀態、快速鍵衝突與語言支援。

### 缺少模式或模型選項

請檢查您的方案、組織原則、專案設定與應用程式版本。

</details>

---

## 重點整理

1. 請保留編輯器來進行深入的程式設計。當代理程式的工作需要更清楚的執行與檢視場所時，開啟 GitHub Copilot app，並隨時跳回您的編輯器。
2. 應用程式是圍繞工作介面來組織的：New 用於開始工作、Pull requests 與 Issues 用於 GitHub 項目、Dashboards 用於您的 GitHub 工作看板、Automations 用於可重複的任務、Customize 用於擴充應用程式，而 Sessions 用於專案與聊天。
3. **聊天工作階段**用於探索。**專案工作階段**用於專注的存放庫工作。**Projects**（公開預覽版）協調朝向長期目標的工作。**Automations** 用於可重複的代理程式執行。
4. **Interactive**、**Plan** 與 **Autopilot** 會改變自主程度。
5. 在提示方塊下方選擇模型與推理強度。簡單任務請保留預設值；複雜工作可考慮較高的強度。

## 下一步

在下一章，您將解決本章提到的「共用工作副本」挑戰：使用 worktree 的隔離工作階段，以及搭配 `@`、`#` 和 `/` 的專注脈絡。

**[← 返回第 00 章](../00-setup/README.zh-Hant.md)** | **[繼續前往第 02 章 →](../02-sessions-worktrees-context/README.zh-Hant.md)**

---

## 來源參考

- [GitHub Copilot app 入門][getting-started]
- [使用代理程式工作階段][agent-sessions]
- [使用 Projects][projects]
- [選擇模型與推理強度][model-selection]
- [GitHub Copilot app 變更記錄][app-changelog]
- [GitHub Copilot app 中的語音聽寫][voice-input]
- [AI 模型參考][ai-models]
- [Copilot Fusion 文件][copilot-fusion]

[getting-started]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/getting-started
[agent-sessions]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions
[projects]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/working-with-projects
[model-selection]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions#choosing-a-model
[app-changelog]: https://github.com/github/app/blob/main/changelog.md
[voice-input]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions#using-voice-dictation
[ai-models]: https://docs.github.com/en/copilot/reference/ai-models
[copilot-fusion]: https://gh.io/github-copilot-fusion
