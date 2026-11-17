# GitHub Copilot app UI Map

- app_version：1.1.32（選單、側邊欄、Dashboards、Copilot Fusion、問題篩選器、Settings 和自動化）；Customize 為 1.1.30；其他區段為 1.1.26
- captured：2026-09-30，並於 2026-10-01 針對 1.1.26 再次檢查（macOS、`demo`
  persona，已開啟 Streamer Mode）
- privacy：專案名稱、工作階段名稱、提取要求標題和帳戶名稱
  都已遮蔽為 `<placeholder>`。請使用
  [map-app.sh](../sample_codes/macos-accessibility/map-app.sh)，從已清理的
  帳戶重新產生，然後在提交前再次遮蔽。

這是一份事實參考資料，用於讓課程步驟和螢幕擷取畫面符合應用程式實際的
UI。選單一律可以對應。其他區段是在以處理程序為範圍的 persona 中，使用
Accessibility 檢查的。應用程式更新後，請重新執行 `map-app.sh`
並與本檔案比對差異。

選單、側邊欄、Dashboards、Copilot Fusion、問題篩選器、Settings 和
自動化控制項，已於 2026-10-24 在 `demo` persona 中再次檢查。

## 選單

- **GitHub Copilot**：About GitHub Copilot · Settings… · Check for Updates… · Services · Hide… · Quit
- **File**：New Session · New Session from Recent · New Chat · Open URL From Clipboard · Create from Local Folder or Repository · Create from GitHub · Create from URL · Close Window
- **Edit**：Undo · Redo · Cut · Copy · Paste · Select All · Writing Tools · AutoFill · Start Dictation… · Emoji & Symbols
- **View**：Toggle Sidebar · Toggle Panel · Toggle Terminal · Command Palette · Back · Forward · Actual Size · Zoom In · Zoom Out · Enter Full Screen
- **Window**：Minimize · Zoom · Bring All to Front
- **Help**：Documentation · Keyboard Shortcuts · What's New · Manage Copilot Subscription · Automations · MCP Servers · Skills · Share Feedback · Run Health Check · Show Home Tips Again · Credits

## 導覽程序（首次執行）

- 登入畫面：**Sign in to GitHub** · **Sign in to GitHub Enterprise Cloud
  (*.ghe.com)** · Accessibility settings。如果應用程式已有帳戶，
  則會改為顯示 **Continue as @`<login>`** 和 **Use a different account**。
- **Connect your repositories. We've selected these based on your activity.**：
  Add local repositories · Select from GitHub（建議的存放庫，未
  選取）· "Nothing selected yet. Repositories can be added anytime." ·
  **Continue**。
- 1.1.25 和 1.1.26 沒有佈景主題步驟。**Continue** 會開啟 New 檢視，並可能出現
  一次性的 **Customize** 提示。GitHub Docs 仍可能描述
  佈景主題步驟。
- 應用程式將狀態儲存在 `app_state` 資料表中，位於
  `<persona home>/.copilot/data.db`（索引鍵 `copilot-onboarding`，欄位
  `hasCompletedOnboarding`）。若要在 `demo` persona 中再次查看導覽程序，
  請關閉它，備份 `data.db`、`data.db-wal` 和 `data.db-shm`，將該欄位設為
  `false`，然後重新啟動。應用程式會將該欄位設為 `true`，時機是
  導覽程序結束時。

## 側邊欄（導覽）

- Toggle sidebar（核取方塊）· Search · Back · Forward · Resize sidebar
- **Quick links**：New · Pull requests · Issues · Dashboards · Automations · Customize。
  **Dashboards** 出現在 1.1.32 的檢查中，但不在 1.1.30 的對應中。
  在 1.1.30 中，**More** 按鈕（**Edit sidebar...**）已移除，而 Settings 也
  沒有隱藏 Quick links 的選項。
- **Sessions** 標題（1.1.30 之前名為 **Projects**）：**Configure sessions** · **New repository or session** (+)
  - Configure sessions：Grouping · Ordering · Show (Project, Branch) · Status ·
    PR · Environment · Source · Reset filters · Collapse all · Mark all as read
  - New repository or session (+)：Chat · "Start session in" `<repository>`（每個
    可見專案一列）· Add GitHub repository · Clone repository · Open folder。
    其頁尾顯示 "New session, Command + N. New chat, Command + Shift + N."
  - **Chats** 列，滑鼠停留時會有 **New chat** (+)。在 1.1.26 中，只有在至少存在
    一個聊天時才會出現該列。**Show** 和 **Edit sidebar** 都沒有
    相關選項。請使用 **+** > **Chat** 開始第一個聊天。
  - 每個已連線專案一列。滑鼠停留時：**Create from…**（"Create project
    from pull requests, branches, or issues in `<project>`"）和 **New session
    in `<project>`** (+)。按右鍵：New session · Create from… · Show in
    Finder · Open on GitHub · Settings · Hide · Customize… · Manage sessions ·
    Remove repository
  - 工作階段列（按右鍵）：Rename · Pin · Mark as unread · Show in Finder ·
    Copy · Open in… · Create nested session · Archive · Delete。聊天列
    沒有 Open in… 或 Create nested session，但多了 Share as secret gist。
- **User profile and settings**（底部）："`<user>`, open user menu" · Share feedback · Settings

## Pull requests 和 Issues 檢視

- 標題和 **Open detail panel**。Issues 另有 **New issue**。
  1.1.32 的標頭不再顯示 **All repositories** 選擇器。
- Pull requests 索引標籤：Authored by me · Assigned to me · Involves me · Review
  requests · Done · New view (+)
- Issues 索引標籤：Assigned to me · Created by me · Mentioning me · Done · New view (+)
- **Filter: `<tab>`**（篩選圖示）會顯示或隱藏查詢方塊，具有 Simple 和
  Query 模式。預設值：`state:open author:@me`（Authored by me）和
  `state:open assignee:@me`（Assigned to me）。
- 在 **Query filter mode** (`<>`) 中，將 `repo:<owner>/<repo>` 加入現有
  查詢，即可將結果限制在單一存放庫。文字欄位名為
  **Edit filter: `<tab>`**。切換索引標籤時，請檢查查詢。較舊的版本
  有獨立的存放庫選擇器，含 **All repositories** 和 **Custom**。
- Pull request 詳細資料：New session (+ New session options: New session, Chat) ·
  Review (Comment, Approve, Request changes) · Ready to merge · Close detail
  panel；索引標籤 Overview 和 Changes。檢查失敗時會顯示 "Some checks were not
  successful"、"Failing (n)"，以及選項選單（Open on GitHub、Re-run）。
  工作階段檢閱面板中的 PR 索引標籤會多出 **Check status** 標題，
  其下有 **Fix failing checks**（選項：Fix with instructions）。
- **Ready to merge**（PR 標頭和工作階段標頭中的合併就緒按鈕）
  會開啟 **Merge pull request** 面板：**Agent merge** 切換開關
  （"Automatically address reviews, fix CI failures, and resolve conflicts."）、
  就緒狀態標題、含 **Fix failing checks** 的檢查，以及註解狀態。
  擷取期間請勿按下合併控制項。
- Issue 詳細資料：New session (+ options) · Assignees · Labels · Type · Add
  property · Create sub-issue · Close issue · Comment

## Dashboards 檢視

已在 1.1.32 檢查。側邊欄的 **Dashboards** 項目位於 Issues 和
Automations 之間。

- 索引標籤群組 **Dashboards**，每個儀表板一個選項按鈕索引標籤（預設為 **My
  Work**）和 **New dashboard** (+)。右上角：**Add column**（圖示）和
  **Add widget**（"Add widget, Command + Shift + A"）。較小的控制項："Add
  column between columns N and N+1" 和 "Add widget after `<widget>`, column N"。
- **Add widget** 選單：資料來源 GitHub、Sentry、Datadog 和 Slack。
  GitHub 清單包含 Issues、Pull requests、Automations、Merge queue、Actions、
  Deployments、Code scanning、Trending repositories 和 Trending developers。
- **My Work** 在三欄中有五個小工具：Pulls created 和 Pulls
  mentioned；Issues assigned 和 Issues mentioned；Requested reviews。項目
  列會顯示標題和編號、存放庫、存在時間、檢查圖示（提取
  要求）和頭像按鈕（"`<user>`, show username"）。
- 每個小工具標頭都有 **Select items** · **Filter** · **Options for
  `<widget>` widget**（Rename · Duplicate · Delete，以紅色顯示）。**Select items**
  會在每個項目加上核取方塊。選取一個項目時會顯示 "1 selected"、**Open in
  new session**、**Session opening options** 和 **Clear**。**Filter** 會開啟
  "`<widget>` filters"（Simple 和 Query filter 模式、Search…、State、Author、
  Sort、Add filter）。空的小工具會顯示 "No pull requests match these
  filters." 或 "No issues match these filters." 以及 **Edit filters**。
- 再按一次 **Filter** 即可關閉其快顯視窗。按下儀表板索引標籤不會
  關閉它。小工具可用鍵盤移動（Enter 或 Space，然後使用方向鍵）。
- 擷取期間請勿按下 **Open in new session** 或 **New dashboard**。
  **New dashboard** 的結果尚未檢查。

## Automations 檢視

- 標題含 **New automation**。空白狀態："Set up automations"、
  **Start automating**、範本卡片，以及 Skills 清單，其中每個技能都有一個
  **New automation** 按鈕。
- 自動化存在之後：Search automations… · Templates · New automation；
  **Your automations** 卡片（Trigger、說明、Environment、上次執行、Run）
  和 **Recent runs**。
- New automation 表單：Name · Trigger（預設為 **Daily**；Manual, Hourly,
  Daily, Weekly, CRON, Issue, Automation failed, Automation completed, Code
  scanning alert, Dependabot alert, Discussion comment, Discussion opened,
  Discussion updated, Pull request, Secret scanning alert, Sub issue added,
  Workflow；共 17 項，因此最後幾項需要捲動）· Hours
  （00:00–23:00，預設為 09:00；可選取多個小時）·
  Minute（:00、:15、:30、:45）· Run in the cloud · Prompt（Mode 預設為
  Autopilot、模型）· 存放庫選擇器（"Select repository"；"Without a repository,
  this automation will run as a chat."）· 選擇存放庫後會出現 **Workspace** 選擇器
  （New worktree、Current checkout）· Cancel · Create + "Create
  automation options" → Create and run。**Create** 在提示有文字之前
  會保持停用。
- CRON 觸發程序：**Expression** 欄位 min · hour · day · month · weekday，每個
  都有提示、"Evaluated in local time."、預覽（例如 "Runs at 8:00 AM
  every day."），以及錯誤（例如 "Invalid CRON, Minute must be 0 to 59."）。
- Run in the cloud 會加入 **Add another trigger**、**Tools**、**Type**（Personal
  或 Shared）和 **AI credit limits**。**Tools** 一開始顯示 **Select
  tools**。其清單有 **Search tools…** 以及 Issues、Pull Requests、
  Discussions、Repos、Actions、Labels、Code Security 和 Automations 群組。選取
  Read PR、List PRs 和 Search PRs 後會顯示 **3 tools selected**。清單會依可用空間
  在按鈕上方或下方開啟。**Type**
  Personal（預設）："Only you can see this
  automation. AI credits are charged to your user. Actions taken on GitHub use
  your identity." Shared："Visible to everyone with access to this repository.
  AI credits are charged to the repository owner. Actions taken on GitHub use
  Copilot's identity." 在選取合適的存放庫之前，表單會顯示 "No models available"。
  此次檢查中未建立任何雲端自動化。
- 自動化詳細資料：名稱按鈕會開啟 **Automation details**（Tokens、Context、
  Session spend、Environment、Schedule、Next run、Runs、Edit automation、Disable
  automation、Delete automation）· Open as chat · Edit · Run automation。Edit
  會開啟 **Edit automation**，含 **Delete**、**Cancel** 和 **Save
  automation**；**Delete** 會先詢問 "Delete automation?"，再移除
  自動化。在 1.1.32 中，該快顯視窗約 340 CSS px 寬，其
  **Repository** 值不會換行，因此較長的名稱（例如
  `AI-Agent-Sandbox/copilot-app-for-beginners`）會超出右邊緣。未經任何處理的
  原始視窗擷取畫面也會顯示相同的溢位。這是應用程式的
  版面配置，而非擷取或清理程序造成的影響。

## Customize 檢視

已在 1.1.30（2026-10-17）檢查。舊的索引標籤（Featured · MCP · Plugins ·
Skills · Extensions · Canvas · Installed）已被取代。

- 索引標籤（**Customize sections** 中的 AXRadioButton）：**Browse** · **Installed**。
  右上角：**Add**（AXPopUpButton "Add extension"：MCP Server… · Plugin… ·
  Skill · Canvas from URL… · Manage marketplaces…）。使用 **Skills** 篩選器時，
  也會出現 **Skill folders** 按鈕。
- 一個搜尋欄位加上 **Filter by type**（AXPopUpButton "Filter by type:
  `<type>`"）：All types · MCP（"External tools and data for agents."）·
  Plugins（"Preconfigured Copilot capabilities."）· Skills（"Resources for
  specialized tasks."）· Extensions（"Extensions from plugins or direct
  installs."）· Canvas（"Agent-driven workflow interfaces."）。預留位置文字
  會隨篩選器變動（"Search MCP servers"、"Search plugins"、"Search skills"、
  "Search installed …"）。搜尋文字和篩選器會在索引標籤之間保留。
  請使用 AXPress 按下篩選按鈕；AXShowMenu 不一定能開啟它。
- Browse：**Editor's picks** 卡片（徽章 Canvas 或 Skill；Install 或 Enable），
  接著是 **All integrations**，含類別索引標籤（All · Plan · Build · Review ·
  Test · Ship · Monitor · Design · Knowledge · Data · Communication）。MCP 列
  有 **Add server, `<name>`**；外掛程式列有 **Install `<name>`**。已安裝的
  項目在 Browse 中也會顯示其 Actions 選單、狀態和切換開關。
- MCP **Add server** 會開啟以伺服器名稱為標題的對話方塊（About、View
  on GitHub、Configuration：Server name、Server type Local/HTTP/SSE、URL、
  Headers、OAuth Client ID、Timeout；Cancel · Add server）。已安裝的伺服器
  會顯示 **Connected**（綠色勾選圖示）、已啟用的切換開關，以及 Actions
  （Edit configuration · Remove；Remove 會詢問 "Remove `<name>`?"）。
- 外掛程式 "view details" 會開啟含 About 和 "This plugin is not
  currently installed." 的對話方塊（Cancel · Install）。它不會顯示來源
  存放庫或市集。Browse 中不會將市集分組；搜尋
  可直接找到外掛程式。**Manage marketplaces…** 有 Source 欄位
  （"owner/repo, Git URL, or local path"）、**Add** 和 Marketplaces 清單
  （awesome-copilot、Default）。
- Installed：區段 **MCP Servers**（Add server）· **Plugins**（Update all、
  Add plugin；列含 Actions 和切換開關）· **Extensions**（Browse
  extensions）· **Canvas**（Built-in Editor、Browser、Terminal，然後是外掛程式
  和 Personal canvas，每個都有 "Open in new session"）· **Skills**
  （Browse skills）。技能列會在名稱下方顯示來源，例如
  "Project: `<project>`"，並附切換開關。沒有 All · Project · Built-in
  篩選器。

## Session composer

- AXTextArea **Message** — 預留位置："Ask anything. Use / for commands, @ files, & sessions, # issues..."（在 Plan 模式中："Plan a task. …"）
- AXPopUpButton **Add context & more** · **Mode: `<mode>`** · 模型和
  推理（例如 "GPT-6.1 Sol · Medium"）· Set up voice dictation · Send message
- Mode 選單：Interactive（"Step-by-step collaboration"）· Plan（"Plan first,
  execute when ready"）· Autopilot（"End-to-end execution without
  interruption"）· **Tool permissions**（子選單：Always ask · Assisted
  (Experimental) · Approve all）。新的 1.1.26 設定檔預設為 **Approve
  all**（`app_state` 中的 `permission-mode-default-on`）。
- 模型和推理選單（1.1.32）：**Copilot Fusion** 切換開關。啟用時，
  **Effort level** 提供 **Standard**、**High** 和 **Max**，接著是
  **Learn more**（`https://gh.io/github-copilot-fusion`）。針對課程設定，composer 會顯示
  **Copilot Fusion · Standard**。關閉切換開關
  即可以 **Model** 選擇個別模型；**Effort** 和 **Context
  window** 取決於所選模型。測試後，請還原原本的模型選擇
  和努力程度。
- AXComboBox **Project: `<project>`**（或 **Chat**）
- AXPopUpButton **Workspace: `<choice>`, branch: `<branch>`** — 選單標題
  **Where to work**：New worktree（"Separate directory for each session"）·
  Current checkout（"Work in the existing checkout"）· Cloud（"Runs in a cloud
  sandbox"）· Base branch（子選單）。標籤顯示為 "New worktree · main"。
  應用程式會在各檢視和重新啟動之間記住上次的選擇。**Create from**
  一律會建立新的 worktree。
- **Agent: `<agent>`** 選擇器只有在載入自訂代理程式時才會出現（選單：
  Default agent，然後是自訂代理程式）。`/agent`（"Choose a custom agent for this
  session."）會列出 Default 和自訂代理程式。新的代理程式檔案需要重新啟動
  應用程式後才會出現。
- 在 Plan 模式計畫之後，**Review plan** 會取代 composer。選項：
  Approve and implement this plan（切換為 Autopilot）· Exit plan mode and
  I will prompt myself（回到 Interactive）· Suggest changes to the plan…
  （文字欄位）· Cancel · Continue。每個選項都會顯示簡短徽章，而其
  Accessibility 標籤是徽章、句號，加上選項文字。徽章在
  各版本之間已有變動，因此請依文字尋找選項，並且不要在課程文字中使用
  徽章。以滑鼠點選選項會立即提交。AXPress
  只會選取選項，之後 **Continue** 或 Return 才會提交所選的
  選項（預設為第一個選項）。計畫也會在檢閱面板的 **Plan** 索引標籤
  中開啟。代理程式通常會先詢問 **Question**（回答
  選項、自由文字回答欄位、Skip、Continue）。
- composer 上方的項目：**Changes +n −n**（Accessibility 標籤 "Open
  changes, Command + Backslash"；它會開啟 **Changes (n), +n, -n** 索引標籤）、
  **PR #n** 和 **Background n**。**Background n** 會開啟 **Background
  activity**：Running 和 Completed 工作、每個執行中工作的 Stop 按鈕，以及
  已分離工作上的 **Detached** 標籤。已附加且執行中的工作會讓回合保持
  開啟。
- `/context` 會開啟使用量快顯視窗：**Plan**（或在 Streamer Mode 下為 **Usage hidden**）、
  **Session** AI credits、**Tokens** 和 **Context**，
  接著是 **View usage and plan...**。展開 **Context** 即可查看其明細。
  它需要進行中的對話：作為新工作階段的第一則訊息時，
  **Send** 和 Return 都沒有作用（1.1.26）。在標頭中選取工作階段名稱，
  即可查看工作分支和基底分支、Remote control、Path、Project、Session name
  和 Session ID。
- `/agent` 只有在載入自訂代理程式之後，才會出現在 `/` 清單中。

## 工作階段檢視和檢閱面板

- 標頭：`<session title> · <repo>/<branch>`（長分支名稱會以
  省略符號縮短）· Run · Open in Visual Studio Code · Open in other apps ·
  當分支有變更時顯示 **Create PR**（含 Create PR options）
- **Changes** 索引標籤工具列（1.1.26）：左側為範圍選單（"Uncommitted n files, changes
  scope"：Committed · Uncommitted）；右側為
  `<branch>` 的分支動作（Commit changes · Push changes · Rename branch）· Collapse all
  files · Diff view options · Show file tree。
- **Toggle panel, Command + Option + B** 會開啟檢閱面板（View 選單
  項目為 **Toggle Panel**）。在 New 檢視中，該選單項目
  可見且已啟用，但不會有任何作用，因此請先提交第一個提示（或使用
  **Create from**），再開啟面板。新的面板會顯示清單：
  Changes · Browser · Terminal · Files · Canvas。**Maximize panel, Command +
  Option + E** 可將其放寬。
- **Add tab, Command + T** (+)：Changes · Terminal · Browser · Files · Side chat ·
  Insights · Canvas。**Canvas** 會開啟子選單，列出已安裝的 canvas，然後是
  "Discover more"：Import canvas from gist/URL · Import canvas from repo。在
  工作階段的第一個提示之前，子選單只會顯示匯入項目。
- 側邊欄工作階段列 > 按右鍵 > **Delete** 會開啟 **Delete session?**
  （"Permanently deletes `<name>` and all its files (about n MB)"、"Uncommitted
  changes are backed up to a recovery branch before deletion"、Don't ask
  again、Cancel、Delete session）。之後應用程式會移除工作階段分支。
  從 **Create from** > `main` 建立的工作階段全都名為 **main**。請依
  其 Accessibility 標籤（包含 "Last updated …"）識別列。
- Browser 索引標籤：URL 欄位、Light/Dark 預覽佈景主題、**Pick & Polish,
  Command + Shift + C**。
- **Resize panel** 分隔線（AXValue 30–80）在取得焦點時，
  會回應向左/向右方向鍵。

## New 內容

- 聊天或所選專案的 Composer
- 專案選擇器：Search · Chat · 已連線的專案 · Add GitHub repository · Clone repository · Open folder
- 三張會在每次造訪之間變動的建議提示卡片
- 頁尾："GitHub Copilot uses AI. Check for mistakes."

## Settings 對話方塊

- 標準類別：General · Accounts · Sessions · Themes · Accessibility ·
  Voice dictation · Customize · Model providers · Experimental，然後是
  **Repositories** 清單。存放庫頁面有 **Show in sidebar** 和沙箱設定。
- General 類別可以顯示目前的應用程式版本。請從
  課程螢幕擷取畫面中移除該版本，因為它經常變動。
- Sessions > **Default branch prefix** 有 **Reset default branch prefix**
  按鈕，可還原為 `%username%-`。

## 課程注意事項

- 在對應或擷取之前，請先啟用並驗證 Streamer Mode。它會隱藏
  公眾無法存取的未發行功能。
- 應用程式的文件仍可能寫成 "My work"。請以應用程式為準：側邊欄有
  各自獨立的 **Pull requests** 和 **Issues** 檢視。預設的儀表板在
  **Dashboards** 檢視中，名為 **My Work**。
- **Chats** 和已連線的存放庫會顯示在 **Sessions** 之下。
- **Chats** 列上的 **+**（**New chat**），以及每個專案列上的 **+** 和 **Create
  from** 圖示，只有在指標位於該列上方時才會出現。未經滑鼠停留所擷取的螢幕擷取畫面
  不會顯示這些控制項，因此請勿將它們回報為遺失。課程步驟
  應告知學習者先指向該列。
  **Sessions** 標題旁的 **+** 則一律可見。
- Sessions **+** 選單的 **Start session in** 群組會列出可見的
  專案。首次執行的學習者還沒有專案，因此請隱藏專案
  （Settings > project > Show in sidebar）以顯示其選單，然後再將其顯示。
- 專案上的 **New session** 會開啟 New 檢視，並選取該專案。
  工作階段會在您提交第一個提示時建立。
- `07-automations/assets/app-automation-schedule.webp` 顯示 **Automation
  details** 快顯視窗，其較長的 **Repository** 值在應用程式中會超出卡片邊緣。
  課程影像會在卡片右邊緣前的最後五個字元
  （約 48 px）讓該文字淡出，並從乾淨的一列還原邊緣
  和陰影像素。如果重拍後仍有此溢位，請套用
  相同的淡出。如果應用程式已將文字保留在卡片內，則不要加入。
- 在 **Create from** 對話方塊中，若搜尋沒有符合任何分支，按下
  Return 會以輸入的文字建立新的本機分支。
  按下 Return 之前，請檢查搜尋文字。
