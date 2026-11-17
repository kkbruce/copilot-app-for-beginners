# 詞彙表

本課程所用初學者術語的快速參考。

## Agent Merge

進階的收尾工作流程。選取 pull request 頂端的合併就緒按鈕（例如 **Ready to merge**），即可開啟含有 **Agent merge** 切換開關的 **Merge pull request** 面板。它可以讀取 pull request、處理阻礙因素，並在 GitHub 允許時進行合併。請在您瞭解差異內容、測試、必要的審查與分支保護規則之後，再使用它。

## Automation

一項已儲存的代理程式工作，可依需求執行、依排程執行，或在進階情況下由存放庫事件觸發。Automation 應具備清楚的提示、受限的工具與審查途徑。

## Autopilot

一種工作階段模式，代理程式會以高度自主性運作。請在瞭解範圍之後，將它用於定義明確、低風險的工作。

## Branch

git 中一條具名的工作線。分支讓您可以進行變更，而不影響主線，直到您準備好合併為止。

## Canvas

在工作階段右側面板中開啟的共用互動式看板。您與代理程式都可以更新它。您可以安裝 canvas（例如從外掛程式安裝），也可以使用 `/create-canvas` 並描述您想要的內容來建立。在本課程中，您會先試用社群 canvas，然後建立工作階段看板與 Feature Workbench。

## Chat

輕量的 GitHub Copilot app 對話，用於提問、腦力激盪或探索存放庫。側邊欄會在 **Chats** 底下列出您的聊天。聊天不會建立分支或 worktree，因此在變更程式碼之前進行探索是安全的。

## CI check

在 pull request 或分支上執行的自動化檢查。它可能會執行測試、組建、程式碼檢查或安全性掃描。CI 代表持續整合（continuous integration）。

## Cloud sandbox

由 GitHub 代管的環境，代理程式可在其中遠離您的本機電腦進行工作。可用性取決於方案、原則、存放庫設定與權限。

## Copilot Fusion

一種模型控制選項，會為每個要求選擇模型與執行工作流程，然後傳回單一回應。它不是單一模型，也不是工作階段模式。其投入程度等級為 **Standard**、**High** 與 **Max**。請參閱[第 01 章的 Copilot Fusion](./01-tour-the-app/README.zh-Hant.md#copilot-fusion)。

## Create from

側邊欄中專案名稱旁的控制項，可從分支、問題或 pull request 啟動工作階段。當您將指標移到側邊欄中的專案上時才會出現。

## Custom agent

針對某個角色或工作流程（例如審查、文件或測試）而專門設定的代理程式。其設定檔會定義指引與可用的工具。在 GitHub Copilot app 中，可使用 `/agent` 或代理程式選擇器來選擇。[第 04 章](./04-skills-custom-agents/README.zh-Hant.md#custom-agents-define-a-role-and-its-tools)包含一個小型的唯讀範例。

## Diff

檢視兩個版本的檔案之間有哪些變更。在本課程中，diff 是您在接受代理程式的成果之前，檢查其工作的主要地方之一。

## Inner loop

在您電腦上的本機開發循環：在變更送到 GitHub 之前，先進行審查、偵錯、測試與預覽。

## Interactive

一種工作階段模式，您與代理程式會逐步進行工作。相較於 Autopilot，代理程式會更常等待您的輸入。

## Issues

列出與您有關的 GitHub 問題的側邊欄檢視。**Assigned to me** 與 **Created by me** 等索引標籤可篩選清單。在 **Query filter mode** 中加入 `repo:OWNER/REPOSITORY` 詞彙，即可將清單限制為單一存放庫。可從問題啟動工作階段，或選取 **New issue** 來建立問題。

## Model

用於回應或工作階段的 AI 系統。不同的模型在速度、成本、推理能力與輸出風格上可能有所不同。您可以選擇個別的模型，或使用 [Copilot Fusion](#copilot-fusion) 為工作選擇模型與工作流程。

## Model Context Protocol (MCP) server

使用 Model Context Protocol 將 Copilot 連接到外部工具與資料的工具伺服器。MCP 伺服器很有用，但可能增加權限、認證資訊與複雜度。在 GitHub Copilot app 中，請從側邊欄的 **Customize** 檢視來管理它們。

## New

應用程式在側邊欄中的登陸檢視。您可以在沒有專案的情況下開始聊天、選取已連接的專案以開始專案工作階段、選擇模式與模型，或從建議的提示開始。

## Outer loop

同一工作流程中屬於 GitHub 的部分：在 **Issues** 與 **Pull requests** 檢視中尋找工作、從問題啟動工作階段、開啟 pull request，並請 Copilot 處理審查留言與失敗的檢查。

## Pick & Polish

GitHub Copilot app 中的瀏覽器預覽功能。選取 **Pick & Polish**，選擇一個已呈現的頁面元素，應用程式就會將該元素作為脈絡附加到提示中，以進行聚焦的 UI 變更。

## Plan mode

一種工作階段模式，代理程式會先提出計畫。您在審查並核准之後，它才會實作。

## Plugin

可為 GitHub Copilot app 新增功能的封裝擴充功能。外掛程式可能包含自訂代理程式、技能、hooks、MCP 伺服器設定或 LSP 伺服器設定。在應用程式中，外掛程式也可以封裝 canvas 擴充功能。請只啟用工作流程所需的項目。可從側邊欄的 **Customize** 檢視瀏覽並安裝外掛程式。

## PR

Pull request。GitHub 上要求審查並將變更從一個分支合併到另一個分支的請求。

## Project session

連接到存放庫的工作階段。當代理程式需要存放庫脈絡、需要變更程式碼，或必須建立 pull request 等成品時，請使用它。專案工作階段預設會使用新的 worktree。

## Prompt injection

一種風險：不受信任的文字（例如問題的標題或內容）企圖操縱代理程式執行非預期的動作。唯讀工作與最小權限的工具選擇可降低此風險。

## Pull requests

列出與您有關的 GitHub pull request 的側邊欄檢視。**Authored by me** 與 **Review requests** 等索引標籤可篩選清單。在 **Query filter mode** 中加入 `repo:OWNER/REPOSITORY` 詞彙，即可將清單限制為單一存放庫。開啟 pull request 即可閱讀其檢查與留言，或啟動工作階段來處理它。

## Reasoning effort

控制模型在工作上投入多少思考的設定。較高的投入程度有助於處理複雜的工作，但可能較慢或成本較高。

## Review panel

工作階段的側邊面板，用於 diff（**Changes**）、**Terminal**、**Browser** 預覽與其他工作介面。在工作階段中，使用 **View** > **Toggle Panel** 開啟。

## Rubber duck

內建的批評者代理程式，以 `/rubber-duck` 叫用，會在您接受成果之前審查計畫、diff、測試或設計，並指出其弱點。

## Session

GitHub Copilot app 的工作區，代理程式可在其中規劃、編輯、執行命令、檢查 diff 並回報進度。工作階段可能在新的 worktree、您目前的簽出或 cloud sandbox 中執行。

## Skill

協助代理程式處理特定類型工作的可重覆使用指南。在本課程中，存放庫本機的技能是新增專注專業知識且對初學者友善的方式。在 GitHub Copilot app 中，可於 **Customize → Skills** 底下找到它們。

## Worktree

附加到同一個 git 存放庫的第二個工作資料夾。在 GitHub Copilot app 中，工作階段的 worktree 通常有自己的分支，讓平行的工作不會與您的主要簽出發生衝突。
