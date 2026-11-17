![第 04 章：技能與自訂代理程式](assets/chapter-header.svg)

> **如果您能重覆使用審查檢查清單，而不必在每項工作中重複輸入相同的提醒，會如何？**

第 03 章將程式碼變更與其差異、測試、瀏覽器預覽及提取要求連結在一起。本章說明如何儲存該工作流程的部分內容，以便重覆使用。您將先從一個為 `samples/book-app-web` 儲存審查指南的[技能](../GLOSSARY.zh-Hant.md#skill)開始。

您也會建立一個[自訂代理程式](../GLOSSARY.zh-Hant.md#custom-agent)，它能說明範例而不編輯範例。技能提供可重覆使用的工作指引；自訂代理程式則定義角色及其可用的工具。第 05 章涵蓋與外部工具及封裝功能的連線。

## 學習目標

完成本章後，您將能夠：

- 在存放庫指引、技能與自訂代理程式之間做出選擇
- 尋找、更新並使用存放庫的 `book-app-reviewer` 技能
- 建立並選取一個能讀取並說明範例而不編輯範例的自訂代理程式
- 套用一項由技能引導的改進，並以第 03 章的工作流程進行驗證

> ⏱️ **預估時間**：約 60 分鐘

## 先決條件

完成[第 03 章](../03-development-workflows/README.zh-Hant.md)。請使用您的課程分支，以及第 00 章的 Node.js 設定。您不需要合併任何練習用的提取要求。

## 來自錄音室：工作階段的歌譜

演奏者早已熟悉自己的樂器。樂團指揮仍會發下一份歌譜，讓大家以相同的方式演奏*這首*歌。

![Copilot 應用程式中技能的歌譜比喻](assets/song-chart-skills.webp)

技能就是那份歌譜。它為 Copilot 應用程式提供重複性工作的指引。自訂代理程式則是依照歌譜演奏的專家，具有明確的角色及完成工作所需的工具。

您仍然需要檢查結果。歌譜並不保證演出一定出色。

## 核心概念

### 選擇適合工作的功能

這些功能解決的是不同的問題。您不需要為每個技能都準備一個自訂代理程式，也不需要為每個提示都準備一個技能。

| 功能 | 所在位置 | 套用時機 | 本章範例 |
| --- | --- | --- | --- |
| 一次性提示 | 目前的對話 | 當您傳送時 | 針對 Book App 提出單一問題 |
| 存放庫指引 | `.github/copilot-instructions.md` | 此專案中的所有工作 | 保持 Book App 的堆疊與行為不變 |
| 技能 | `.github/skills/<name>/SKILL.md` | 當工作符合其描述或您叫用它時 | 更新並套用 book-app 審查檢查清單 |
| 自訂代理程式 | `.github/agents/<name>.agent.md` | 當您以 `/agent` 或代理程式選擇器選取它時 | 建立唯讀的 book-app 說明員 |

指引是家規。技能則是某一類歌曲的歌譜。當您重複進行同一類工作時，請使用技能：

<img src="assets/skill-vs-one-off-prompt.webp" alt="一次性提示與技能的比較：技能是存放在存放庫中的可重覆使用檢查清單，每次都提供相同的指南" width="800" />

請在側邊欄的 **Customize** 檢視中尋找技能。請使用 `/agent` 或提示方塊中的代理程式選擇器來選取自訂代理程式。

> [!TIP]
> **選用探索：** 完成本章的練習後，您可以瀏覽 Awesome Copilot 目錄，查看社群建立的[代理程式][awesome-agents]與[技能][awesome-skills]範例。在新增或使用其他自訂項目之前，請先檢閱該代理程式的工具或該技能的指引。

> [!TIP]
> **安裝外部技能：** 您可以直接在應用程式中安裝來自存放庫以外的技能。在 **Customize** 中，選取 **Add**，然後選取 **Install skill…**。您可以從本機資料夾、ZIP 檔案、GitHub 技能資料夾 URL 或 HTTPS ZIP 連結進行安裝。在將任何內容寫入您的電腦之前，應用程式會顯示檢閱步驟，因此您可以在安裝前查看該技能包含的內容。這是試用來自 [Awesome Copilot][awesome-skills] 目錄的社群技能的快速方式，無需手動複製檔案。

> [!IMPORTANT]
> 技能會告訴 Copilot 如何處理工作，但其指引並不會強制限制 Copilot 可以執行的操作。例如，寫著「審查但不編輯」的技能並不會停用檔案編輯工具。技能也可能包含指令碼，或指示 Copilot 使用工具，因此在啟用不熟悉的技能之前，請先閱讀其內容。本課程的審查技能僅包含指引。

## 建立練習工作階段

1. 選取您課程專案旁的 **Create from**。
2. 選取 **Branches**，然後選取 `main`，以在新的工作樹中開始工作階段。
3. 將模式設定為 **Interactive**。
4. 提交下列提示：

   ```text
   Show this session's branch and worktree path. Confirm that .github/skills/book-app-reviewer/SKILL.md and samples/book-app-web exist here. Do not edit files, commit, or push.
   ```

請在所有練習中保留此工作階段。當您在編輯器中開啟檔案時，請使用此工作階段的工作樹，而不是原始的複製品。

**預期輸出：** 工作階段會顯示與您原始複製品分開的工作樹路徑，並找到這兩個路徑。

> [!NOTE]
> 檔案編輯屬於此工作樹。已安裝的工具範圍可能較廣，並影響其他工作階段。從 `main` 建立的新工作樹不會包含此工作階段中尚未提交的編輯。

## 技能：儲存審查檢查清單

技能是一個含有 `SKILL.md` 檔案的資料夾。針對 Book App，本課程在下列位置提供了審查檢查清單技能：

```text
.github/skills/book-app-reviewer/SKILL.md
```

該檔案以兩條 `---` 線之間的中繼資料開頭。此區塊稱為 **YAML 引言**。`description` 可協助 Copilot 應用程式決定何時載入該技能：

```markdown
---
name: book-app-reviewer
description: Review changes in samples/book-app-web for accessibility, responsive layout, tests/build validation, and small beginner-safe changes.
---
```

引言下方的 Markdown 包含指引。此技能涵蓋堆疊、測試、建置、協助工具、回應式版面配置及變更大小。

存放庫技能可透過 Git 共用。個人技能則適用於您電腦上的所有專案。如需支援的位置，請參閱[關於代理程式技能][agent-skills]。

### 練習：更新並使用課程技能

您將新增兩條具體的審查規則，然後使用更新後的技能。此時您還不會變更範例應用程式。

HTML 標題層級（`h1`、`h2` 與 `h3`）描述的是頁面結構，而不只是文字大小。替代文字會向無法看見的使用者傳達圖片與圖示的意義。螢幕閱讀器可以朗讀這些替代文字。

1. 選取 **Customize**（標註 1），然後選取 **Installed** 索引標籤（標註 2）。

1. 將類型篩選設定為 **Skills**（標註 3），並搜尋 `book-app-reviewer`。

1. 確認結果顯示 **Project: copilot-app-for-beginners**，且其切換開關為開啟（標註 4）。請忽略課程製作用的技能。

   ![Customize 的 Installed 索引標籤，含有 Customize、Installed、Skills 類型篩選及 book-app-reviewer 結果的標註](assets/app-customize-skills.webp)

1. 返回您的章節工作階段並提交：

   ```text
   Read @.github/skills/book-app-reviewer/SKILL.md. Explain the frontmatter and summarize the Review focus list. Do not edit files or run checks.
   ```

1. 提交這個有範圍限制的編輯要求：

   ```text
   Edit only @.github/skills/book-app-reviewer/SKILL.md. Append these two items to Review focus:

   - Check that headings describe the page structure: one page-level h1, section headings below it, and book titles below the results heading.
   - Check that informative images and icons have text alternatives. Decorative images should use empty alt text, and decorative icons should be hidden from assistive technology.

   Keep all existing rules and frontmatter unchanged. Do not edit the sample app, commit, or push.
   ```

1. 檢查 **Changes** 索引標籤。確認 `SKILL.md` 中只新增了這兩條規則。

1. 提交 `/skills reload` 以載入已編輯的技能。

1. 將模式設定為 **Plan** 並提交：

   ```text
   Use the book-app-reviewer skill to inspect @samples/book-app-web/src. Focus on the results heading, book-title headings, and empty state.

   Apply the two rules we added. Propose at most one small improvement and name the files it would affect. If no change is needed, explain why.

   List the review rules you applied. Separate code observations from tests or browser checks that still need to run. Do not edit files or run commands.
   ```

1. 展開回應中的技能活動，確認 Copilot 應用程式已載入 `book-app-reviewer`。將計畫與檔案中的規則進行比較。請在實作之前停止。

**預期輸出：** 計畫引用了新增的標題或替代文字規則，並指向具體的程式碼。在原始範例中，書名與結果標題都使用 `h2`。如果 Copilot 應用程式建議將書名改用 `h3`，其計畫也必須考量 `.book-card h2` CSS 選取器。計畫不得聲稱測試或瀏覽器檢查已通過。

**運作方式：** 變更後的檔案提供了可重覆使用的檢查清單。指名技能能讓您的意圖更明確，但並不保證發現的結果正確。請檢查活動與程式碼，而不只是相信 Copilot 應用程式所說它已遵循規則。

> [!TIP]
> 輸入 `/` 以尋找可用的命令。該技能應顯示為 `/book-app-reviewer`。一般的審查提示也可能自動載入相符的技能，因此答案不同並不能證明某次審查使用了技能而另一次沒有。

檢查清單定義了如何審查程式碼。接下來，您將定義另一種角色：一個能說明程式碼，但沒有工具可變更程式碼的代理程式。

## 自訂代理程式：定義角色及其工具

修繕房屋時，您不會每次都找同一位通才幫手。您會找水電工、電工或屋頂工。自訂代理程式把同樣的概念套用於程式碼審查、測試、安全性與文件：先定義一次角色，需要時再選取該代理程式。

<img src="assets/hiring-specialists-analogy.webp" alt="水電工、電工與屋頂工說明專家角色，對比於用於程式碼審查、測試、安全性與文件的 AI 代理程式" width="800" />

**自訂代理程式**具有名稱、指引及一組可用的工具。技能向代理程式提供工作流程；自訂代理程式則定義執行工作的角色。自訂代理程式並不是另一種工作階段模式，也不一定是不同的 AI 模型。

例如，審查者可能需要檢查清單與測試工具。說明員只需要讀取與搜尋檔案。您將在此建立這個較小的角色。

代理程式設定檔是含有 YAML 引言的 Markdown 檔案，與技能類似：

| 設定檔部分 | 用途 |
| --- | --- |
| `name` | 在代理程式選擇器中識別該代理程式 |
| `description` | 描述代理程式的用途 |
| `tools` | 限制可用的工具；`read` 與 `search` 允許檢查檔案，而不編輯或執行命令 |
| 引言下方的 Markdown | 定義代理程式的行為與回答格式 |

### 練習：建立唯讀的 Book App 說明員

1. 以 **Interactive** 模式返回章節工作階段。如果提示方塊下方出現代理程式選擇器，請選取 **Default agent**。
2. 將指示與完整的 Markdown 區塊複製到同一則訊息中，然後傳送：

   ```text
   Create .github/agents/book-app-explainer.agent.md with exactly the Markdown below. Create the agents folder if it is missing. If the file exists, show it and stop instead of replacing it. Do not edit other files, commit, or push.
   ```

   ```markdown
   ---
   name: book-app-explainer
   description: Explain the Book App's code and tests to a beginner without changing files.
   tools: ["read", "search"]
   ---

   Explain files in samples/book-app-web.
   Read the relevant code before answering. Do not guess from file names.
   Use short sentences and define new technical terms.
   Do not edit files, run commands, or use external services.
   Do not claim that tests passed; you can inspect tests but cannot run them.

   Structure each answer as:
   1. What it does.
   2. How the data moves, with file paths and function names.
   3. One small example from the current book data.
   4. What the existing tests cover and one useful manual check.
   ```

1. 在 **Changes** 中檢查新檔案。確認 `tools` 只包含 `read` 與 `search`。
1. 載入新的代理程式。應用程式會在啟動時載入自訂代理程式。請等所有工作階段中進行中的工作完成，然後結束 GitHub Copilot 應用程式並重新開啟。關閉視窗與結束應用程式不同。接著在側邊欄中選取本章的工作階段。
2. 在提示方塊中輸入 `/agent`。
3. 從代理程式清單中選取 **book-app-explainer**，然後傳送完整的 `/agent book-app-explainer` 命令。確認提示方塊下方的代理程式選擇器顯示 `book-app-explainer`。如果仍然找不到 `/agent` 或該代理程式，請依照[已變更的技能或新的代理程式遺失](#a-changed-skill-or-new-agent-is-missing)操作，然後再試一次。

   <img src="assets/app-custom-agent-picker.webp" alt="/agent 建議清單，其中 book-app-explainer 已反白，並可看到其唯讀描述" width="800" />

   此螢幕擷取畫面顯示的是命令傳送之前的選取狀態，因此代理程式選擇器仍顯示 **Default agent**。傳送命令後，請以即時的代理程式選擇器作為檢查依據：它應顯示 **book-app-explainer**。只有在有可用的自訂代理程式時，代理程式選擇器才會出現在提示方塊下方。

1. 在選取該代理程式的狀態下，提交：

   ```text
   Explain how @samples/book-app-web/src/App.tsx keeps reading statistics aligned with filtered books. Use the Unread filter as your example. Follow your four-part answer format.
   ```

1. 將答案與 `App.tsx`、`ReadingStats.tsx` 及現有的測試進行比較。確認該代理程式沒有變更檔案或執行命令。
2. 在進行作業之前，請使用 `/agent` 或代理程式選擇器返回預設代理程式。

**預期輸出：** 答案會追蹤 `filters` 如何經由 `filterBooks` 成為 `filteredBooks`，然後說明該清單如何傳遞到 `ReadingStats` 與書籍卡片。它將測試涵蓋範圍與建議的手動檢查區分開來。建立設定檔之後，沒有任何檔案變更。

**運作方式：** Markdown 定義了角色與回答格式。`tools` 清單限制了可用的工具；若省略它，則會允許所有可用的工具。指示中的資料夾範圍僅用於引導代理程式，並不是檔案系統沙箱。

此代理程式無法執行測試，也無法儲存自己的報告。這項限制是刻意的：讓代理程式的工具與其工作相符，且不要多加任何額外的工具。當您需要這些動作時，請使用預設代理程式。

> [!TIP]
> 自訂代理程式完成回應後，可能會建議後續步驟。這些建議會以預先填入的提示呈現，您可以在傳送之前檢閱並編輯。您可以使用建議順暢地切換到另一個代理程式，或繼續對話，而不必重新輸入您的意圖。

### 檢查點

完成這些練習後，您應該已有：

- 一個已更新的 `book-app-reviewer` 技能，其中含有您新增的審查規則。
- 一個 `book-app-explainer` 自訂代理程式設定檔，將代理程式限制為只能使用讀取與搜尋工具。

---

## 疑難排解

如果某些功能未如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出所有章節的問題與解決方法。

<details>
<summary>技能與自訂代理程式問題</summary>

### 已變更的技能或新的代理程式遺失

請確認您已將檔案儲存在此工作階段的工作樹中。檢查路徑與 YAML 引言。對於技能，請嘗試 `/skills reload`。對於新的代理程式或找不到重新載入命令的情況，請等所有工作階段中進行中的工作完成。結束並重新開啟 GitHub Copilot 應用程式，然後返回同一個工作階段。關閉視窗與結束應用程式不同。請不要為了重新載入尚未提交的檔案而再次從 `main` 開始。

### 來自 Copilot 應用程式的通用建議

請從 `/` 功能表叫用該技能，或明確指名它。展開活動以查看它是否已載入。要求提供特定檔案的證據及所套用的規則。僅憑措辭不同，並不能說明技能是否發揮了作用。

### 我找不到技能或自訂代理程式

請使用側邊欄中的 **Customize** 檢視。自訂代理程式是以 `/agent` 或提示方塊中的代理程式選擇器來選取。標籤與可用的命令可能因應用程式版本而異。

### 自訂代理程式無法執行測試

此範例只允許讀取與搜尋。請返回預設代理程式以執行命令。請不要只為了讓唯讀練習能運作而新增殼層存取權。

</details>

---

## 重點整理

1. 存放庫指引為 Copilot 應用程式提供整個專案工作的共用規則。技能提供針對特定工作（例如程式碼審查）的可重覆使用指引，Copilot 應用程式可在需要時載入。
2. 自訂代理程式在 Copilot 應用程式中定義專家角色，具有自己的指引與可用的工具。讓其工具與其工作相符：只說明程式碼的代理程式需要讀取與搜尋工具，而不需要編輯或殼層存取權。
3. 技能提供工作指南，而不是權限邊界。自訂代理程式的工具清單限制了它可以採取的動作。
4. 不要只依賴 Copilot 應用程式所說它使用了某項自訂項目或已完成某項工作。請對照檔案檢查其活動與結果。對於程式碼變更，請執行測試並檢查應用程式。

## 作業

![作業](../assets/assignment.webp)

練習停在審查與說明。現在請使用一項經過審查的建議，完成第 03 章的內部迴圈。

1. 留在同一個工作樹並使用預設代理程式。選擇技能的標題建議。如果審查發現不需要變更，請改為針對目前的標題結構新增一個專注的測試。
1. 將模式設定為 **Plan**。輸入 `/`，選取 `/book-app-reviewer`，將您的建議加入此提示，然後提交：

   ```text
   Plan only the recommendation I select from this chapter. Name the source, CSS, and test files it affects. Preserve filtering, statistics, and the existing design. Include test, build, and browser checks. Do not implement yet.
   ```

1. 在 Copilot 進行規劃時，記錄基準。在檢閱面板的 **Terminal** 索引標籤中，從存放庫根目錄執行這些命令：

   ```bash
   cd samples/book-app-web
   npm install
   npm test -- --run
   npm run build
   ```

   請記下測試數量與建置結果。在您變更應用程式之前，兩者都必須通過。

1. 檢閱計畫並選取 **Exit plan mode and I will prompt myself**。如果 **Review plan** 方塊持續開啟，請選取 **Continue**。確認模式為 **Interactive**，然後要求 Copilot 應用程式只實作該項變更，不要進行不相關的重構或新增相依性。
1. 以第 03 章的內部迴圈驗證變更：
   - 檢查 **Changes**，然後重新執行 `npm test -- --run` 與 `npm run build`。請勿弱化現有的測試。
   - 以 `npm run dev` 啟動應用程式，並在 **Browser** 索引標籤中開啟其 **Local** URL。在寬與窄兩種寬度下檢查標題。請 Copilot 應用程式確認所呈現的標題層級，因為單憑外觀無法看出標題層級。
   - 搜尋 `hobbit`，然後搜尋 `zzzz-no-match`。確認統計資料與每個結果相符，且空白狀態訊息提到變更搜尋字詞、類型或閱讀狀態。
   - 以 `Ctrl+C` 停止開發伺服器。
1. 要求預設代理程式儲存一份簡短的記錄：

   ```text
   Create samples/book-app-web/docs/chapter-04-review.md. If it exists, update it without removing prior notes.

   Record the skill rules applied, the custom agent's role and tool limits, the change made, and only the commands and browser checks that actually ran. If a check was blocked, record why. Change only this document. Do not commit or push.
   ```

**成功準則：** 您的差異包含技能更新、代理程式設定檔、一項專注的應用程式或測試變更，以及審查記錄。測試與建置皆通過。您能說明存放庫指引、技能與自訂代理程式之間的差異。

如果您想保留這項工作，請在使用第 03 章的提取要求工作流程之前，先檢閱完整的差異。否則，請將它保留在此工作樹中作為練習。第 05 章會從 `main` 開始新的工作階段，且不需要合併這些變更。

## 下一步

您現在已有可重覆使用的審查指引，以及一個小型的唯讀代理程式。兩者都會自訂 Copilot 應用程式處理您專案的方式。

在第 05 章中，您將擴充應用程式可存取的範圍。您將連接用於文件的 MCP 伺服器，並安裝提供可重覆使用技能的外掛程式。

**[← 返回第 03 章](../03-development-workflows/README.zh-Hant.md)** | **[繼續前往第 05 章 →](../05-mcp-plugins/README.zh-Hant.md)**

---

## 來源參考

- [自訂 GitHub Copilot 應用程式][customizing]
- [關於代理程式技能][agent-skills]
- [為 Copilot CLI 建立及使用自訂代理程式][custom-agents]
- [自訂代理程式設定與工具別名][agent-config]
- [GitHub Copilot 應用程式的斜線命令][slash-commands]
- [Awesome Copilot 代理程式][awesome-agents]
- [Awesome Copilot 技能][awesome-skills]

[customizing]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/customize-github-copilot-app
[agent-skills]: https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
[custom-agents]: https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/en/copilot/reference/custom-agents-configuration
[slash-commands]: https://docs.github.com/en/copilot/reference/github-copilot-app-reference/slash-commands
[awesome-agents]: https://awesome-copilot.github.com/agents/
[awesome-skills]: https://awesome-copilot.github.com/skills/
