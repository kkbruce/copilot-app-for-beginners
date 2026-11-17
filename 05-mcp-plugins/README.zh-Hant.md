![第 05 章：MCP 伺服器與外掛程式](assets/chapter-header.svg)

> **如果代理程式能查閱它所需的文件，而不是只依賴它已知的資訊，會如何？**

第 04 章介紹了技能與自訂代理程式：可重覆使用的指引與已定義的角色。本章將透過 **Model Context Protocol (MCP) 伺服器**與**外掛程式**，擴充 GitHub Copilot 應用程式可存取的範圍。

您將使用 Context7 擷取最新的文件，然後安裝一個提供網頁指南技能的外掛程式。

## 學習目標

完成本章後，您將能夠：

- 說明什麼是 MCP 伺服器，以及它與外掛程式有何不同
- 連接 MCP 伺服器，並確認其工具會傳回有用的資訊
- 安裝外掛程式並叫用其中一項技能
- 將擷取到的指南與本機程式碼進行比較，而不必將該程式碼傳送到外部服務
- 檢閱權限，並區分已完成的工具活動與代理程式未獲支援的說法

> ⏱️ **預估時間**：約 45 分鐘

## 先決條件

完成[第 04 章](../04-skills-custom-agents/README.zh-Hant.md)，讓您熟悉技能、自訂代理程式及 **Customize** 檢視。請使用您的課程分支，以及第 00 章的 Node.js 設定。您不需要將第 04 章的變更合併到本章。

Context7 練習需要網際網路存取，以及新增 MCP 伺服器的權限。外掛程式練習需要安裝外掛程式及使用其文件工具的權限，該工具透過 Node.js 執行。不需要雲端供應商訂閱或部署。

如果您的組織封鎖了某項整合，請閱讀其範例與預期輸出，記下您無法執行，然後繼續進行另一項練習。兩項練習互不相依。

## 來自錄音室：連接設備

歌譜會告訴樂手該演奏什麼，但它不會接上麥克風，也不會在混音台上加入效果。當工作階段需要另一項功能時，您才會選擇並連接設備。

MCP 伺服器就像與該設備的連線：它公開代理程式可以使用的工具。外掛程式則像是您可以安裝的一組封裝附加元件。在啟用之前，請先檢查其中包含什麼，以及它需要哪些存取權。

<img src="assets/extending-copilot-app.webp" alt="技能與代理程式自訂 Copilot 應用程式的工作；MCP 伺服器與外掛程式則擴充其功能" width="800" />

## 核心概念

### MCP 伺服器與外掛程式的比較

| 功能 | 新增的內容 | 本章範例 |
|---|---|---|
| MCP 伺服器 | 將 Copilot 應用程式連接到服務或資料來源的工具 | 使用 Context7 擷取 React 的輸入標籤指南 |
| 外掛程式 | 可安裝的技能、代理程式或其他功能的套件 | 使用 Modern Web Guidance 規劃一項小型的 CSS 改進 |

外掛程式可以包含 MCP 伺服器，但兩者並不能互換。您可以直接連接伺服器，而外掛程式也可以在不新增伺服器的情況下提供技能。

請在側邊欄的 **Customize** 檢視中尋找這兩者。請從您已有的工具開始；只有當整合能提供工作所需的內容時，才新增它。每多一個工具，就會讓代理程式多一分雜訊，也多一項需要檢閱的存取權。

<img src="assets/least-tool-principle.webp" alt="一個工具箱中只有 Repo、Docs 和 Skill 被反白：只給代理程式它所需要的，以減少雜訊" width="700" />

## 建立練習工作階段

1. 選取您課程專案旁的 **Create from**。
2. 選取 **Branches**，然後選取 `main`，以在新的工作樹中開始工作階段。
3. 將模式設定為 **Interactive**。如果提示方塊下方出現代理程式選擇器，請確認它顯示 **Default agent**，而不是第 04 章的唯讀說明員代理程式。
4. 提交：

   ```text
   Show this session's branch and worktree path. Confirm that samples/book-app-web/src/components/BookFilters.tsx, samples/book-app-web/src/components/BookCard.tsx, and samples/book-app-web/src/styles/app.css exist here. Do not edit files, run app checks, commit, or push.
   ```

**預期輸出：** 工作階段會識別其工作樹，並找到這三個檔案。

請在兩項練習與作業中保留此工作階段。對於這些唯讀的比較，您不需要安裝 Book App 的相依性，也不需要執行其開發伺服器。

> [!NOTE]
> 工作樹分隔的是檔案變更，而不是已安裝的工具。MCP 連線與外掛程式可能會影響其他工作階段。在新增任一者之前，請先檢查它是否已安裝並啟用；請勿建立重複項目。

## MCP 伺服器：從另一項服務取得資訊

瀏覽器擴充功能可以將您的瀏覽器連接到服務，例如密碼保管庫。MCP 伺服器為 Copilot 應用程式提供與工具及資料類似的連線。在這裡，Context7 提供對 React 文件的存取。

<img src="assets/browser-extensions-analogy.webp" alt="瀏覽器擴充功能將瀏覽器連接到服務，就像 MCP 伺服器將 Copilot 應用程式連接到 GitHub、檔案與 Context7 文件一樣" width="800" />

Model Context Protocol 定義了 AI 應用程式如何連接到工具與資料。**MCP 伺服器**提供這些工具。伺服器可以在您的電腦上執行，也可以在遠端服務上執行。

此圖形顯示的是範例連線，而不是必要的安裝項目。應用程式已具備讀取與搜尋本機檔案的工具。請在 **Customize** 中將類型篩選設定為 **MCP**（而不是 **Extensions**）來設定 MCP 連線。

當工作需要目前工具無法提供的資訊或動作時，請使用 MCP。對於第 03 章的問題與提取要求工作，您不需要再新增另一個 GitHub 伺服器。

[Context7][context7] 是具有 MCP 伺服器的文件服務。在此範例中，它提供 React 文件。它不會成為 Book App 的相依項目。

<img src="assets/app-customize-mcp.webp" alt="Customize 的 Installed 索引標籤，含有 MCP 篩選與 context7 搜尋，顯示一個已連線且已啟用的 Context7 伺服器" width="800" />

此螢幕擷取畫面顯示設定後的一個 Context7 連線。在這項練習之前，您在 **Installed** 索引標籤上的 **MCP Servers** 清單可能是空的。每項服務請使用一個連線。

### 練習：使用 Context7 檢查 Book App 的輸入標籤

在 React 中，標籤的 `htmlFor` 值可以與輸入的 `id` 相符，以將標籤連結到該輸入。

首先，請在 GitHub Copilot 應用程式中設定好 Context7 MCP 伺服器。如果已連線，請略過新增伺服器的步驟。

1. 開啟 **Customize**，然後選取 **Browse** 索引標籤。將類型篩選（**All types**）設定為 **MCP**。
2. 搜尋 `context7`，然後在 **Context7** 列上選取 **Add server**。
3. 檢閱 **Context7** 設定對話方塊中的值：

   | 設定 | 值 |
   |---|---|
   | 伺服器名稱 | `context7` |
   | 伺服器類型 | `HTTP` |
   | URL | `https://mcp.context7.com/mcp` |

   `HTTP` 伺服器類型表示 Copilot 應用程式會連線到託管的伺服器，而不是啟動本機處理序。

   <img src="assets/app-context7-configuration.webp" alt="Context7 MCP 伺服器對話方塊，伺服器名稱為 context7、已選取 HTTP，URL 為 https://mcp.context7.com/mcp" width="800" />

1. 選取 **Add server**。
2. 選取 **Installed** 索引標籤。確認 **Context7** 出現在 **MCP Servers** 之下，並帶有已連線圖示與已啟用的切換開關。
3. 返回您先前建立的章節工作階段。請使用 **Interactive** 模式與預設代理程式。
4. 提交：

   ```text
   Use the Context7 MCP tools to find the official React documentation for input labels.

   Ask only this general question: How do nested labels compare with htmlFor and id when labeling an input?

   Summarize both patterns and include the source links. Do not send repository code or paths to Context7. If the MCP tools are unavailable, report that and stop.
   ```

1. 展開工具活動。確認 **Context7** 工具傳回了文件。開啟來源連結，並將其與摘要比較。
2. 提交：

   ```text
   Compare the retrieved guidance with @samples/book-app-web/src/components/BookFilters.tsx. Keep this comparison local. Explain whether Search, Genre, and Status have associated labels. Do not edit files or make more external requests.
   ```

**預期輸出：** MCP 活動包含文件結果。本機比較會找出巢狀於 `<label>` 元素內的輸入與選取項目。當控制項位於其標籤內時，缺少 `htmlFor` 並不是缺陷。不應有任何原始檔案變更。

**運作方式：** 伺服器透過工具呼叫提供文件。代理程式接著將該結果與本機程式碼進行比較。讓外部問題保持一般性，您就能使用文件而不必將存放庫內容傳送給該服務。

## 外掛程式：使用封裝的功能

您可以分別新增技能與 MCP 伺服器，也可以將它們作為**外掛程式**的一部分安裝。外掛程式可以包含技能、自訂代理程式、MCP 伺服器或畫布擴充功能。**市集**是外掛程式的目錄。安裝外掛程式與叫用其中的技能或代理程式是不同的。

請選擇提供您所需功能且持續維護的外掛程式。請閱讀其內容，而不只是市集的描述。有些外掛程式會啟動處理序、呼叫服務或執行攔截程序，這些是由代理程式事件觸發的命令。

> [!TIP]
> **選用探索：** 完成本章的練習後，您可以瀏覽 [Awesome Copilot 外掛程式目錄][awesome-plugins]，尋找其他社群外掛程式。本練習請使用 `modern-web-guidance`。在安裝其他外掛程式之前，請檢閱其來源、包含的功能及所需的存取權。

### 練習：使用 Modern Web Guidance 規劃 CSS 改進

來自 [GoogleChrome/modern-web-guidance][modern-web-guidance] 的 `modern-web-guidance` 外掛程式提供一項搜尋網頁平台指南的技能。您將以它處理一個書名版面配置的問題，而不是重新設計。此練習不需要先完成 Context7 練習。

該技能使用 `npx` 來下載並執行其文件工具。需要 Node.js 與網際網路存取。使用前請檢閱[技能指引][modern-web-skill]及任何命令核准。

> [!NOTE]
> 企業管理的設定可以限制 GitHub Copilot 應用程式中可用的外掛程式與市集。如果您無法安裝 `modern-web-guidance`，或無法存取 `awesome-copilot` 市集，請閱讀範例與預期輸出，將該練習記錄為受阻，然後繼續。請勿為了完成練習而變更公司管理的設定。

1. 在 **Customize** 中，選取 **Browse** 索引標籤（標註 1）。將類型篩選設定為 **Plugins**（標註 2）。
2. 搜尋 `modern-web-guidance`（標註 3）。如果沒有出現結果，請選取 **Add** > **Manage marketplaces…**。確認 **awesome-copilot** 在 **Marketplaces** 清單中。如果遺失且您的原則允許，請在 **Source** 中輸入 `github/awesome-copilot` 並選取 **Add**。

   <img src="assets/app-customize-plugins.webp" alt="Customize 的 Browse 索引標籤，含有 Browse、Plugins 類型篩選、modern-web-guidance 搜尋及 Install 的標註" width="800" />

   此螢幕擷取畫面顯示安裝前的外掛程式。安裝後，外掛程式會出現在 **Installed** 索引標籤的 **Plugins** 之下，且其啟用切換開關為開啟。

1. 選取 **modern-web-guidance** 以開啟其詳細資料並檢閱描述。詳細資料不會顯示來源，因此請與[外掛程式存放庫][modern-web-guidance]（`GoogleChrome/modern-web-guidance`）比較。然後，如果尚未安裝，請選取 **Install**（標註 4）。
2. 選取 **Installed** 索引標籤。確認外掛程式出現在 **Plugins** 之下，且其啟用切換開關為開啟。
3. 以 **Interactive** 模式與預設代理程式返回專案工作階段。提交 `/skills reload`，然後輸入 `/` 並選取已安裝的 `modern-web-guidance` 技能。顯示的命令可能包含外掛程式前置詞。加入此提示：

   ```text
   Use the modern-web-guidance skill from the installed plugin. Look up guidance for wrapping a short heading across multiple lines. Use only general terms in external queries.

   Then inspect @samples/book-app-web/src/components/BookCard.tsx and @samples/book-app-web/src/styles/app.css locally. Propose at most one CSS-only improvement for long book titles on narrow screens.

   Name the guide used, explain browser support, and give a browser check. Keep the current design and dependencies. Do not add JavaScript, change files, or run app checks. If the current CSS needs no change, explain why. If the plugin skill is unavailable, stop.
   ```

1. 檢查技能活動及其指南搜尋輸出。確認建議指的是目前的書名元素與 CSS。像 `text-wrap: balance` 這樣的建議，必須說明在不支援它的瀏覽器中會發生什麼事。

**預期輸出：** Copilot 應用程式會載入外掛程式的技能並擷取相關指南。它會傳回一項小型的 CSS 提案，或保留現有版面配置的理由。Book App 檔案維持不變。

**運作方式：** 與您在第 04 章編輯的存放庫技能不同，此技能是透過已安裝的套件而來。您仍然需要叫用它、檢查其工具活動，並對照程式碼檢查其建議。僅僅安裝並不能證明技能已執行。

## 疑難排解

如果某些功能未如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出所有章節的問題與解決方法。

<details>
<summary>MCP 與外掛程式問題</summary>

### 我在設定中找不到 MCP 或外掛程式

請使用側邊欄中的 **Customize** 檢視。標籤與可用的命令可能因應用程式版本而異。

### MCP 伺服器已啟用，但其工具失敗

請檢查連線狀態、URL、驗證、網路存取及組織原則。僅憑啟用的切換開關並不能證明伺服器已連線。每項服務請使用一個連線。如果服務回報速率限制，請等待，或使用其核准的驗證流程。請勿將認證放入提示或存放庫檔案中。

### 外掛程式已安裝，但其技能遺失

請檢查外掛程式的啟用切換開關與目前的內容。執行 `/skills reload`，然後在 `/` 功能表中搜尋技能名稱，包括任何外掛程式前置詞。如果仍未出現，請等所有工作階段中進行中的工作完成，結束並重新開啟應用程式，然後返回此工作階段。關閉視窗與結束應用程式不同。

如果外掛程式不再提供該技能，請將該練習記錄為受阻，而不是安裝不相關的外掛程式。

### 代理程式無法使用該整合

如果提示方塊下方出現代理程式選擇器，請確認它顯示 **Default agent**，而不是第 04 章的唯讀說明員。接著請檢查提示方塊下方模式功能表中的 **Tool permissions**，並檢查組織原則。請勿只為了完成練習而擴大代理程式的工具或變更原則。

</details>

---

## 重點整理

1. MCP 伺服器提供透過已連線的服務擷取資訊或執行動作的工具。
2. 外掛程式封裝了技能、代理程式、MCP 伺服器或畫布擴充功能等功能。安裝它並不會自動執行其內容。
3. 檢閱每項工具能做什麼，以及它傳送了哪些資料。一般的文件問題不需要存放庫程式碼。
4. 檢查工具活動、擷取到的指南及任何傳回的來源連結，而不只是代理程式所說整合已運作。
5. 只新增您需要的功能。工作樹不會將已安裝的工具與其他工作階段隔離。

## 作業

![作業](../assets/assignment.webp)

彙整這些練習的證據。您不需要再進行程式碼變更或提取要求。

1. 在同一個工作階段中，詢問預設代理程式：

   ```text
   Summarize this chapter's Context7 and Modern Web Guidance exercises in a short table. For each, name the capability used, the evidence that it ran, the source evidence, and what we learned about the Book App.

   For source evidence, include a returned source link when one is available. If the tool returned a guide without a source link, include the guide name and ID instead. Do not invent a link.

   Separate completed work from proposed browser checks. If an exercise couldn't run, state why and do not invent results or sources. Use only this session's existing evidence. Do not edit files, run commands, or make more external requests.
   ```

2. 將表格與工具活動、擷取到的指南及來源進行比較。更正任何未獲支援的說法。在檢閱面板中開啟 **Changes** 索引標籤，確認它未列出任何已變更的檔案。
3. 用您自己的話說明，為何 Context7 連線是 MCP 伺服器，而 Modern Web Guidance 是提供技能的外掛程式。
4. 如果您不想繼續使用，請只停用您為此練習所新增的 MCP 連線或外掛程式。請保持原本已存在的工具不變。第 06 章不要求任一整合保持啟用。

**成功準則：** 您能區分 MCP 連線與外掛程式，找出您所使用的每項整合的工具與來源證據，並說明哪些內容仍留在本機。如果某項整合受阻，您的摘要會如實說明，而不是聲稱它已執行。沒有任何 Book App 檔案變更。

## 下一步

您現在知道如何連接外部工具並使用封裝的功能，而不會將安裝與成功執行混為一談。

在第 06 章中，您將使用畫布，讓工作階段的計畫、進度與驗證證據保持可見。您將從探索透過外掛程式安裝的社群畫布開始，以本章的安裝工作流程為基礎。

**[← 返回第 04 章](../04-skills-custom-agents/README.zh-Hant.md)** | **[繼續前往第 06 章 →](../06-canvases/README.zh-Hant.md)**

---

## 來源參考

- [自訂 GitHub Copilot 應用程式][customizing]
- [為 Copilot CLI 新增 MCP 伺服器（應用程式也使用）][mcp-setup]
- [Context7 來源與設定][context7]
- [React：為輸入提供標籤][react-input-label]
- [關於 GitHub Copilot 外掛程式][plugins]
- [Modern Web Guidance 來源][modern-web-guidance]
- [Modern Web Guidance 技能指引][modern-web-skill]
- [GitHub Copilot 應用程式的斜線命令][slash-commands]
- [Awesome Copilot 外掛程式][awesome-plugins]

[customizing]: https://docs.github.com/en/copilot/how-tos/github-copilot-app/customize-github-copilot-app
[mcp-setup]: https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
[context7]: https://github.com/upstash/context7
[react-input-label]: https://react.dev/reference/react-dom/components/input#providing-a-label-for-an-input
[plugins]: https://docs.github.com/en/copilot/concepts/agents/about-plugins
[modern-web-guidance]: https://github.com/GoogleChrome/modern-web-guidance
[modern-web-skill]: https://github.com/GoogleChrome/modern-web-guidance/blob/main/skills/modern-web-guidance/SKILL.md
[slash-commands]: https://docs.github.com/en/copilot/reference/github-copilot-app-reference/slash-commands
[awesome-plugins]: https://awesome-copilot.github.com/plugins/
