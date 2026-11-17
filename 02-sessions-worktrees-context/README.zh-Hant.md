![第 02 章：工作階段、工作樹與脈絡](assets/chapter-header.svg)

> **如果每項任務都有自己的工作區、分支、脈絡與歷程記錄呢？**

在第 01 章中，你看過「共用工作複本」的問題：兩項代理程式任務可能會在同一個資料夾與分支中混在一起。工作階段正是 GitHub Copilot app 不再只是一般聊天的地方。一個工作階段可以擁有自己的分支、工作資料夾、計畫、差異、終端機輸出、瀏覽器預覽與 GitHub 脈絡。在本章中，你將學到工作樹如何讓工作彼此分離。接著，你會從練習分支啟動工作階段，附加一個 issue 作為脈絡，並使用斜線命令來檢視該工作階段。

## 學習目標

完成本章後，你將能夠：

- 從分支啟動以工作樹為基礎的工作階段，並附加 issue 作為脈絡
- 說明什麼是 git 工作樹，以及它如何讓每個工作階段的變更不影響你的 `main` 簽出
- 為工作階段加入相關脈絡，讓 Copilot 能理解任務及其輔助資訊
- 使用多種方式附加脈絡：輸入 `@` 附加檔案、輸入 `#` 附加 issue，或從電腦拖放檔案
- 在目前的簽出、隔離的工作樹或雲端沙箱之間做出選擇
- 使用 `/chronicle standup` 與 `/context` 來檢視工作階段

> ⏱️ **預估時間**：約 30 分鐘

## 先決條件

完成第 [00](../00-setup/README.zh-Hant.md) 章與第 [01](../01-tour-the-app/README.zh-Hant.md) 章。此時，你已連接課程存放庫，並了解聊天與專案工作階段之間的差異。

## 來自錄音室：一間錄音室，多間錄音間

想像一首歌需要三位音樂家（主唱、吉他、鼓）同時錄製。為了獲得最佳效果，你不會讓他們擠在同一支麥克風旁，然後祈禱一切順利。你會讓每個人待在各自的隔音錄音間，單獨錄製不同的聲部，或是平行錄製（以取得更多「現場」的感覺），之後再把各軌混音在一起。這種做法讓每一條錄製的音軌都能單獨編輯與修改。

![以錄音間比喻工作樹與聚焦的脈絡](assets/recording-booths-worktrees.webp)

工作樹就像獨立的錄音間。它連接到同一個存放庫（同一首歌），但擁有自己的資料夾與分支，因此平行的工作不會互相衝突。

## 核心概念

### 什麼是 Git 工作樹？

**Git 工作樹**讓你可以為同一個存放庫建立額外的工作目錄。每個工作樹通常會簽出到不同的分支（或提交）。

這讓你可以同時處理多項任務或多個分支，而不必暫存變更，或在單一資料夾中不斷切換分支。

### GitHub Copilot app 為何使用工作樹

| 沒有隔離 | 使用以工作樹為基礎的工作階段 |
|---|---|
| 多項任務可能編輯同一個資料夾 | 每項任務都有獨立的資料夾 |
| 容易搞不清楚分支狀態 | 工作階段分支會顯示在 app 中 |
| 測試與差異可能混在一起 | 差異會與工作階段綁定 |
| 較難比較工作內容 | 較容易檢查與核准 |

![一個存放庫，多個安全的工作樹](assets/one-repo-many-worktrees.webp)

由於每個工作階段都有自己的工作樹，你可以平行執行多個工作階段而不會混淆它們的檔案變更：一個工作階段修正錯誤，另一個則探索不同的分支。在本章中，請一次進行一個工作階段。

### 工作階段在哪裡執行

當你使用 **+** 啟動工作階段時，提示方塊下方的工作區選取器可讓你選擇工作在*哪裡*進行。該功能表也有 **Base branch**，用來設定新工作樹起始的分支。

app 會記住你上次的選擇。你在第 00 章選擇了 **Current checkout**，所以現在請切換回來：指向 `copilot-app-for-beginners` 專案，選取 **+**，開啟工作區選取器，然後選擇 **New worktree**。你不需要送出提示；app 會保留此選擇。以 **Create from**（稍後會詳述）啟動的工作階段一律會取得新的工作樹。

![GitHub Copilot app 中的 Where to work 功能表，顯示 New worktree、Current checkout、Cloud 與 Base branch](assets/app-where-sessions-run.webp)

這些選擇需要在速度與隔離之間取捨：

| 工作區 | 意義 | 在下列情況選擇它…… |
|---|---|---|
| New worktree | 工作階段會在你的複製品旁取得自己的資料夾與分支 | 你希望變更、分支與差異與主要簽出分開（本課程採用的安全預設值） |
| Current checkout | 工作階段直接在你現有的複製品中運作，沒有獨立資料夾 | 你想快速、低風險地看一下，且不介意工作階段動到你的工作資料夾 |
| Cloud | 工作階段在 GitHub 託管基礎結構上的雲端沙箱中執行，而不是在你的電腦上。雲端沙箱目前為公開預覽版 | 你想卸載工作，或讓本機環境保持不受影響 |

> [!TIP]
> 如有疑慮，請選擇新的工作樹。它能讓你的 `main` 簽出保持整潔，同時仍在你的電腦上執行，這也是本課程其餘部分多半採用以工作樹為基礎的工作階段的原因。

工作樹分隔的是**檔案與分支**。它們不會分隔你電腦上的所有事物。如果兩個工作階段使用相同的開發伺服器、資料庫與連接埠，仍可能發生衝突。之後當你執行多個 app 預覽時，請使用不同的連接埠。

### 脈絡語法

GitHub Copilot app 讓你能在提示方塊中以幾個特殊字元加入脈絡與命令：

| 語法 | 用途 | 範例 |
|---|---|---|
| `@` | 檔案或資料夾 | `@samples/book-app-web/src` |
| `#` | Issue 或提取要求 | `#12` |
| `/` | 斜線命令 | `/chronicle standup` |
| `&` | 其他工作階段（當提示方塊提供時） | 輸入 `&` 並從清單中選擇一個工作階段 |

> [!TIP]
> 提供最少但有用的脈絡。少即是多。

### 附加檔案

除了輸入 `@` 來參考檔案與資料夾之外，你也可以從電腦拖曳檔案，直接放到側邊欄中的工作階段或代理程式上。當你有準備好要附加、但不屬於存放庫的檔案時，這很有用。放入的檔案會加入工作階段的脈絡，你可以接著繼續輸入提示。

你也可以將檔案放到側邊欄中的 **New** 項目上，以啟動已附加這些檔案的新工作階段。

### 斜線命令

斜線命令是你在提示方塊中輸入的捷徑。它們可以開啟 app 公用程式、叫用代理程式行為、檢查使用量，或觸發已安裝的技能。探索你的 app 支援哪些命令的最安全方式，是在提示方塊中輸入 `/` 並閱讀面板。命令可能因 app 版本、已啟用的外掛程式、已安裝的技能與組織原則而異。

在本章中，你只需要兩個命令：

| 命令 | 用途 | 在下列情況使用它…… |
|---|---|---|
| `/chronicle` | 開啟工作階段歷程記錄功能。`/chronicle standup` 會摘要你過去一天的工作 | 你想回顧最近的工作 |
| `/context` | 開啟使用量快顯視窗，顯示權杖使用量、脈絡詳細資料與 AI 點數花費 | 你想查看工作階段目前保留了多少對話與檔案文字 |

<details>
<summary>選用：你可能看到的其他斜線命令</summary>

| 命令 | 說明 |
|---|---|
| `/agent` | 選取或切換工作階段的作用中代理程式。在你加入自訂代理程式（第 04 章）後才會出現。 |
| `/collect-debug-logs` | 建立用於疑難排解的偵錯記錄封存，或將其上傳為私密 gist。 |
| `/create-canvas` | 使用內建的畫布撰寫技能建立或變更畫布。第 06 章會介紹畫布。 |
| `/new` | 開始全新的對話，類似 `/clear`。 |
| `/orchestrate` | 透過委派給子工作階段，協調多工作階段或多存放庫的工作。 |
| `/research` | 研究某個主題並產生附有引用的報告。 |
| `/review` | 檢閱目前工作階段中的變更。 |
| `/rubber-duck` | 請評論者代理程式檢閱你的做法或實作。評論者使用與你的工作階段不同的模型。 |
| `/skills` | 管理技能。`/skills reload` 會在工作階段期間重新載入技能。 |
| `/usage` | 開啟你方案的使用量與速率限制詳細資料。 |
| `/[skill-name]` | 直接叫用已安裝的技能，例如第 04 章中的 `/book-app-reviewer`。可用的命令取決於你已安裝的技能。 |

如有疑慮，請輸入 `/` 並使用 app 內的面板來探索有哪些可用項目。

</details>

## 練習：從分支啟動工作階段並附加 Issue

**空白狀態**是沒有書籍符合你的篩選條件時所顯示的訊息。你將從一個練習分支啟動工作階段，該分支中的這則訊息被刻意改得較不實用，然後附加對應的 GitHub issue 作為脈絡。如果你已執行 [00 - 設定](../00-setup/README.zh-Hant.md)中的設定指令碼，你分叉的存放庫就已經有該分支與 issue。

請執行下列步驟：

1. 在側邊欄中，指向 `copilot-app-for-beginners` 專案，然後選取它旁邊出現的 **Create from** 圖示。

   <img src="assets/app-create-from-icon.webp" alt="從分支建立工作階段" width="800" />

1. 選取 **Branches** 索引標籤，然後選取 `practice-empty-state-copy`。app 會在新的工作樹中從該分支啟動新的工作階段。

1. 在工作階段提示方塊中，將 **Mode** 設為 **Plan**。

1. 輸入 `#3`，然後從 issue 選擇器中選取 **Improve the empty state copy** 以將其附加到提示。如果你的種子 issue 編號不同，請輸入 `#` 並依標題選取。

1. 在附加的 issue 之後加入下列指引，然後送出提示：

   ```text
   Investigate the issue and create a plan to address it.
   ```

   Copilot 應該會分析該 issue，並產生一份計畫，讓你在進行任何變更之前先檢閱。Copilot 可能會先針對範圍問一個簡短的問題。如果是這樣，請選取建議的答案。

   當計畫準備就緒時，它會在 **Plan** 索引標籤中開啟，且 **Review plan** 方塊會取代提示方塊。你可以在 **Autopilot** 中核准並實作計畫、結束計畫模式並自行撰寫提示，或建議修改計畫。

   ![Review plan 方塊，顯示 Approve and implement this plan、Exit plan mode and I will prompt myself 與 Suggest changes to the plan 選項，位於 Plan 索引標籤中的計畫旁](assets/app-plan-from-issue.webp)

1. 選取 **Exit plan mode and I will prompt myself**。如果 **Review plan** 方塊仍然開啟，請選取 **Continue**。在送出另一個提示之前，請確認工作階段已回到 **Interactive**。

   在進行變更之前，請先執行 Book App 並觀察其目前的行為，作為比較用的基準。

1. 送出此提示：

   ```text
   Run the Book App in samples/book-app-web and open the preview.
   ```

1. 預覽會在內建的瀏覽器面板中開啟。在 book app 的搜尋列中，搜尋 `hobbit`，並確認出現 **The Hobbit**。

1. 將搜尋內容換成 `zzzz-no-match` 以顯示空白狀態。記下它目前的標題與訊息，以便稍後與更新後的版本比較。

   ![瀏覽器索引標籤中的 Book App 預覽，搜尋 zzzz-no-match，顯示 0 本書，空白狀態為 No results, Try again](assets/app-book-app-empty-state-before.webp)

1. 請代理程式實作計畫：`Implement the plan`

   Copilot 運作時，你會在計畫檢查清單中看到即時更新。**Changes** 索引標籤會顯示正在修改之檔案的差異。

1. 選取檢閱面板中的 **Changes** 索引標籤，或提示方塊上方的 **Changes** 標記，以檢查差異。

1. 重新載入瀏覽器索引標籤並再次嘗試相同的搜尋。確認空白狀態訊息建議變更搜尋詞、類型或閱讀狀態。

## 練習：使用斜線命令檢查工作階段

使用[斜線命令](#slash-commands)中的兩個斜線命令，來檢視你剛剛工作的工作階段。

1. 在你一直使用的工作階段提示方塊中，送出下列斜線命令：

   ```text
   /chronicle standup
   ```

   **預期輸出：**Copilot 應該會回報你過去一天的工作。報告應包含此工作階段以及你在其中所做的變更。

   ![Chronicle standup 報告，在 Done 下列出此工作階段的空白狀態文案工作](assets/app-chronicle-standup-output.webp)

1. 接著，送出下列斜線命令，以檢查工作階段的權杖、脈絡與 AI 點數詳細資料：

   ```text
   /context
   ```

   這會在提示方塊下方開啟使用量快顯視窗。你也可以選取提示方塊下方、麥克風圖示旁的 AI 點數圖示來直接開啟。選取 **Context** 列以展開其明細。

   脈絡是 GitHub Copilot app 用於目前工作階段的內容。檢查它有助於你在加入更多檔案、issue 或指引之前，得知工作階段是否快要負荷過重。

   **預期輸出：**GitHub Copilot app 會開啟使用量快顯視窗，並顯示方案、工作階段、權杖與脈絡資訊。

   ![使用量快顯視窗，顯示 Usage hidden、工作階段 AI 點數、權杖總計與展開的 Context 明細](assets/app-context-information.webp)

   此圖顯示的是使用量總計範例。你的總計會有所不同。

   - **Plan** 顯示你已使用方案的百分比。如果 **Streamer Mode** 已開啟，則會改為顯示 **Usage hidden**。
   - **Session** 顯示工作階段所使用的 AI 點數。
   - **Tokens** 顯示工作階段傳送給模型（向上箭頭）與從模型接收（向下箭頭）的權杖數。
   - 展開 **Context** 會顯示系統提示、工具與訊息使用了多少脈絡視窗。顯示的類別可能因模型與工作階段中可用的工具而異。
   - 選取 **View usage and plan...** 以開啟你帳戶的使用量與計費詳細資料。

   > [!NOTE]
   > 選取 Copilot app 視窗頂端的工作階段名稱，即可取得工作階段的工作分支、基底分支、**Remote control**、**Path**、**Project**、**Session name** 與 **Session ID**。

> [!NOTE]
> 此練習分支包含刻意設計的迴歸問題。你不需要建立提取要求，也不需要將修正合併到 `main`，因為 `main` 已包含正確的行為。請在繼續之前，要求 Copilot 停止此工作階段的開發伺服器。

---

## 疑難排解

如果工作階段資料夾、分支或預覽看起來不對，請先參考 [appendices/git-worktrees.md](../appendices/git-worktrees.zh-Hant.md) 與[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)。

<details>
<summary>工作階段、工作樹與脈絡問題</summary>

### 我看不到練習分支

再次執行第 00 章的設定指令碼，或依照 [appendices/training-github-scenarios.md](../appendices/training-github-scenarios.zh-Hant.md) 操作。請確認你連接的是你的分叉，而不是上游的課程存放庫。

### 工作階段編輯了錯誤的資料夾

開啟工作階段詳細資料，檢查工作樹路徑與分支名稱。課程練習請優先使用**新的工作樹**。

### 找不到 `/context` 或 `/chronicle`

`/context` 只有在工作階段至少送出過一個提示後才能運作。在新的工作階段中，請先送出提示，然後再試一次。

輸入 `/` 並使用 app 內的面板。官方清單位於 [Slash commands for the GitHub Copilot app](https://docs.github.com/en/copilot/reference/github-copilot-app-reference/slash-commands)。

### 兩個預覽發生衝突

工作樹隔離的是檔案與分支，而不是連接埠。請停止其中一個 Vite 伺服器，或將第二個伺服器啟動在連接埠 `5174`。

</details>

---

## 重點摘要

1. 工作階段是專注的代理程式工作區，擁有自己的分支、差異與歷程記錄。
2. 工作樹讓工作階段的變更與你的主要簽出分開。新的工作樹是安全的預設值。
3. 工作樹隔離檔案與分支，但不隔離連接埠、資料庫或背景處理序。平行預覽請使用不同的連接埠。
4. `@` 附加檔案與資料夾，`#` 附加 issue 與提取要求，`/` 則執行如 `/chronicle` 與 `/context` 等命令。

---

## 作業

![作業](../assets/assignment.webp)

使用本章的工作流程，在隔離的工作樹中為 Book App 新增淺色與深色佈景主題。先前的練習使用 `#` 附加 issue；這次請使用 `@` 附加代理程式所需的程式碼。請將工作保持在本機。第 03 章會介紹 issue 與提取要求的工作流程。

1. 在側邊欄中，指向 `copilot-app-for-beginners` 專案並選取 **Create from**。在 **Branches** 索引標籤上，選取 `main` 以啟動新的工作樹工作階段。

1. 請 Copilot 執行 `samples/book-app-web` 並開啟預覽。

1. 選取視窗頂端的工作階段名稱以開啟工作階段功能表，並檢查工作階段詳細資料。確認工作分支與 `main` 是分開的，並記下工作樹路徑。

1. 檢查 app，確認它只支援淺色佈景主題。

1. 將工作階段設為 **Plan** 模式。輸入 `@samples/book-app-web/src`，從選擇器中選取該資料夾，並包含下列要求：

   ```text
   Plan a light and dark theme toggle for the Book App using the attached source folder.

   - The user can switch between light and dark themes.
   - The toggle has a clear, accessible label.
   - Text, controls, cards, and backgrounds remain readable in both themes.
   - Search, filters, and reading statistics keep their current behavior.

   Name the files you expect to change and the checks that will show the feature works. Keep the plan small. Do not change any files yet.
   ```

1. 檢閱計畫並選取 **Exit plan mode and I will prompt myself**。如果 **Review plan** 方塊仍然開啟，請選取 **Continue**。確認模式為 **Interactive**，然後請 Copilot 實作計畫。

1. 檢查差異，確認其中只包含佈景主題切換所需的變更。重新載入瀏覽器預覽，並驗證切換鈕能在可讀的淺色與深色佈景主題之間切換。在兩種佈景主題中都試試 `hobbit` 與 `zzzz-no-match`，以檢查書籍卡片與空白狀態。

1. 請 Copilot 執行相關測試與建置。在將變更視為完成之前，請檢查命令輸出。第 03 章會更詳細地說明這些驗證步驟。

1. 送出 `/chronicle standup`。將回顧內容與你觀察到的差異及檢查結果比較。它應該能區分已完成的工作與仍需注意的事項。

1. 請 Copilot 停止此工作階段的開發伺服器。佈景主題功能會保留在它的工作樹中，不會加入 `main`。此作業不需要建立 issue 或提取要求。

**成功準則：**你能夠識別工作階段的分支與工作樹、說明你附加了哪些脈絡，並在不變更 `main` 的情況下展示佈景主題變更及其驗證證據。

## 下一步

在下一章中，你將把隔離的工作階段用於實際的開發工作。內迴圈涵蓋檢閱、偵錯、測試與瀏覽器預覽。外迴圈涵蓋 issue、提取要求、檢閱意見與檢查。

**[← 返回第 01 章](../01-tour-the-app/README.zh-Hant.md)** | **[繼續前往第 03 章 →](../03-development-workflows/README.zh-Hant.md)**

---

## 來源參考

- [About the GitHub Copilot app][about-app]
- [Working with agent sessions][agent-sessions]
- [Slash commands for the GitHub Copilot app][slash-commands]
- [GitHub Copilot app repository][app-readme]
- [GitHub Copilot app generally available][changelog]
- [GitHub Copilot app product blog][app-blog]

[agent-sessions]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions
[slash-commands]: https://docs.github.com/en/copilot/reference/github-copilot-app-reference/slash-commands
[about-app]: https://docs.github.com/en/copilot/concepts/agents/github-copilot-app
[app-readme]: https://github.com/github/app
[changelog]: https://github.blog/changelog/2026-06-17-github-copilot-app-generally-available/
[app-blog]: https://github.blog/news-insights/product-news/github-copilot-app-the-agent-native-desktop-experience/
