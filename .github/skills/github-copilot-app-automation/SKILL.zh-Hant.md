---
name: github-copilot-app-automation
description: 自動化並研究 GitHub Copilot app 桌面體驗。適用於操控 Copilot App 工作階段、建立隔離的 app 實驗、對應 app UI/輔助使用控制項、擷取 app 螢幕擷圖、儲存 PNG 擷取檔、最佳化 WebP 影像、測試提示但不送出、使用 app/工作階段 API、canvas、自動化、macOS Accessibility、AppleScript，以及安全的端對端 Copilot App 工作流程探測。
---

# GitHub Copilot app Automation

當工作內容是**操控、測試、對應或實驗 GitHub Copilot app** 時，請使用此技能。優先使用 app/工作階段 API，其次是 app 原生的 canvas/自動化，只有在必須實際操作可見的桌面 UI 時才使用 GUI Accessibility。

## Where to Run This

此技能的效能很大程度取決於代理程式執行的位置：

| 環境 | 工作階段/app API | 擷取可靠度 | 最適合 |
|---|---|---|---|
| **在 GitHub Copilot app 代理程式內**（建議） | 可用於主機執行個體。它們不會選取另外啟動的 persona。 | 當代理程式依處理序 ID 啟動、操控並擷取 persona 時，可靠度高 | 使用專用 persona 進行端對端螢幕擷圖擷取 |
| **GitHub Copilot CLI**（終端機） | 不可用 | 低/手動 — 必須由人員操作 app，且其視窗必須與終端機位於同一個 macOS Space | 唯讀對應（`map-app.sh`）、動態的 `screenshots.sh` 列出/內嵌流程，以及已可見視窗的一次性擷取 |

對於課程螢幕擷圖，請**在 app 內驅動此流程**，但不要把主機執行個體當作螢幕擷圖目標。
請啟動下方專用的 persona，
然後使用以處理序為範圍的 Accessibility 與擷取。此處的 shell 指令碼在兩種環境下都能執行，
因為 app 代理程式是建立在 Copilot CLI 之上。

無論在哪種環境：像素仍然來自 `screencapture`（目前沒有純算繪/匯出 API，因此視窗必須可見），需要螢幕錄製權限，擷取內容必須來自訓練分支上的**已清理訓練帳戶**，而進階功能（Agent Merge、雲端自動化）仍受原則/計費限制。

對於每張螢幕擷圖，請從持續存在、已登入的
`demo` persona 啟動新的 app 處理序，而不是建立新的 persona 或重覆使用已開啟的處理序：

```bash
SK=.github/skills/github-copilot-app-automation/sample_codes/macos-accessibility
PERSONA="demo"
# 訓練分支的本機複本，而非上游課程儲存庫。
REPO="$HOME/copilot-app-for-beginners"
COPILOT_PID="$(bash "$SK/prepare-persona.sh" "$PERSONA" "$REPO" 60)"
```

這會保留現有的登入與 persona 資料，啟動獨立的 app 處理序，
僅將該視窗設為標準的 1920x1080 點框架，重設顯示器
縮放並放大三次（縮放會經過量測與確認），啟用並
驗證 **Streamer Mode**，驗證帳戶已登入，並在傳回處理序 ID 之前
關閉暫時性橫幅。如果側邊欄中沒有
與所給路徑資料夾名稱相同的儲存庫，前置步驟會透過
原生資料夾選擇器新增該確切資料夾。接著會檢查是否出現
具有該資料夾名稱的儲存庫。它檢查的是名稱而非完整路徑，因此請勿
在 persona 中保留另一個同名資料夾的複本。設定失敗時
會保留該處理序
以供檢查，且不會傳回成功的處理序 ID。當有另一個 Copilot 執行個體開啟時，請勿依 app 名稱選取
目標。請勿使用全螢幕：
它可能會在 app 內容上方繪製功能表列與標題列。

若要在擷取期間繼續使用其他顯示器工作，請為 `prepare-persona.sh` 與
`capture-window.sh` 都設定
`COPILOT_CAPTURE_DISPLAY=builtin`。視窗接著會在內建筆記型電腦顯示器上開啟，
並且因為筆記型電腦的框架較小，只會放大兩次而非三次。
設定完成後，焦點會回到您先前使用的 app。指標
不會移動，擷取也不會啟用 persona。請勿在
啟動的最初幾秒內輸入，並讓筆記型電腦顯示器保持在目前的 Space。

在準備可見狀態之前，先擷取影像參考周圍的
Markdown：

```bash
python3 "$SK/screenshot-context.py" <chapter>/README.md <image-name>
```

對於尚未有影像名稱的待處理 `<!-- app-screenshot: ... -->` 預留位置，
請傳入 `--line <line>`，其值為 `screenshots.sh next` 所列印的行號。

請使用章節標題、影像之前的指示，以及影像之後的指示，
來定義確切的畫面、開啟的功能表、選取的項目與標註。
請勿擷取籠統的近似畫面。請確認目前的 Accessibility
標籤與文字一致。如果 app 標籤已變更，請在擷取之前更新直接
相關的課程文字。

當一張螢幕擷圖顯示兩個或更多讀者必須依序選取的控制項時，
請加入編號的紅色標註。附近的指示必須說明每個
編號，例如：「選取 **+**（標註 1），然後選取 **Add GitHub
repository**（標註 2）。」請勿在單一動作的螢幕擷圖、
輸出範例或佐證螢幕擷圖中加入標註。若要指向沒有順序的單一項目，
例如要選取的功能表項目，請改用紅色醒目提示方框
（`--box`）。若要指向單一的小型狀態或控制項，例如狀態標籤，請使用紅色
箭頭（`--arrow TAIL_X:TAIL_Y:HEAD_X:HEAD_Y`）。重新擷取既有影像時，
請在相同控制項的新位置重新建立其標註、
方框或箭頭。

請保留處理序 ID，並將其用於所有 UI 操作與擷取。請勿使用
`navigate_to` 或其他 app/工作階段 API 來準備此視窗，因為這些
API 可能會路由到代管代理程式的執行個體。請透過範圍限定於其處理序 ID 的
Accessibility 選取器來操控 persona。

隔離的 `HOME` 並不會隔離儲存在 macOS 鑰匙圈中的認證。
persona 仍可能顯示目前的 GitHub 帳戶名稱與大頭貼。
依處理序的擷取會自動從 accessibility 樹狀結構衍生可見的設定檔名稱，
並將該文字取代為 **Copilot Dev**。如果帳戶代號或儲存庫擁有者
與正規化後的設定檔名稱相符，則會
將該文字取代為 **copilotdev**，包括路徑與分支名稱內的文字。
終端機提示與路徑可能顯示的
macOS 帳戶名稱與電腦名稱，會變成 **copilotdev** 與 **copilot-dev-mac**。它會保留大頭貼
以及無關的人員與組織。在 Settings 畫面上，它也會移除
顯示的 app 版本。切勿寫死個人姓名或 app 版本。
切勿僅憑 persona 名稱就認定擷取內容已清理。
請放大檢視每個被取代的片段：部分取代可能會在
新文字旁留下私人名稱的字母。

## Non-Interactive / Hidden-Session Rule

如果使用者要求**全新**、**獨立**或**隱藏**的 Copilot App 執行個體/工作階段：

- 僅使用 app/工作階段 API：`list_projects`、`create_session`、`send_session_message`、`get_session`，以及檔案系統驗證。
- 除非使用者明確要求查看 App UI，否則**請勿**呼叫 `navigate_to`、前景 `open -a`、功能表列動作、資料夾選擇器或 Accessibility 點擊。
- **請勿**要求使用者選取資料夾或專案。如果無法透過可用的非互動式介面註冊專案，請回報此限制，而不是開啟可見的選擇器。
- 對於本機資料夾，必要時請先讓目標資料夾成為獨立的儲存庫根目錄（在目標資料夾中執行 `git init`），讓 App 能將它與上層儲存庫區分開來。
- 如果必須嘗試隱藏式註冊，建議使用 `open -g -j -a "GitHub Copilot" <folder>`，並立即以 `list_projects` 驗證；如果它沒有註冊該專案，請停止並回報此缺口。
- macOS `screencapture` 無法擷取隱藏/最小化/僅在背景的工作階段的螢幕擷圖；螢幕擷圖擷取需要可見的視窗，或未來由 App 提供的算繪/匯出 API。請勿僅為了滿足隱藏螢幕擷圖的要求而切換使用者目前的 App 視窗。

## Operating Model

分層運作，從最安全到最脆弱：

1. **工作階段協調工具**：列出專案/工作階段、建立隔離的工作階段、傳送訊息給工作階段、檢查工作階段、導覽至工作階段。
2. **App 原生介面**：canvas、自動化、專案設定、MCP、技能、issue/PR 工作階段。
3. **Copilot CLI 遠端控制**：適用於透過 GitHub.com/GitHub Mobile 控制 CLI 工作階段，不適用於原生 app GUI 控制。
4. **macOS Accessibility/UI 指令碼**：對具名控制項（例如 `Message`、`Select model` 與 `Open changes`）使用 AppleScript/System Events。
5. **螢幕擷圖擷取**：使用 Accessibility 視窗邊界加上 `screencapture` 儲存 PNG，然後轉換為 WebP 以供網頁傳遞。
6. **低階 GUI 點擊**：除非使用者明確核准且沒有具名的 accessibility 控制項，否則請避免。

## Safety Rules

- 除非使用者明確希望修改目前的工作階段，否則**請為實驗建立獨立的工作階段**。
- 對於隱藏/獨立工作階段的工作，除非明確要求，否則**切勿操作使用者可見的 App 視窗**。
- 沒有使用者明確意圖時，**切勿送出提示、建立 PR、合併、刪除、核准權限或啟用 Agent Merge**。
- 對於 GUI 探測，請從唯讀開始：列舉視窗、控制項、角色、名稱與動作。
- 對於撰寫區測試，請輸入明確標示的草稿，並在不按 Return/Enter 的情況下清除它。
- 避免依像素座標點擊。請依 accessibility 角色/名稱/動作來指定控制項。
- 請勿將機密或私人 UI 資料保存在儲存庫檔案中。請將暫存探測存放在 `/tmp` 或工作階段成品目錄。
- 螢幕擷圖可能包含私人儲存庫名稱、提示、差異、終端機輸出或權杖。僅擷取所要求的 app 狀態，並將可用於課程的影像儲存在刻意指定的 assets 資料夾下。
- 預期會有暫時性的 app/視窗狀態。在每次 UI 操作之前，請重新探索視窗與 `AXWebArea`。
- 如果 Accessibility 被拒絕，請停止並要求使用者授予自動化呼叫端存取權。請勿嘗試繞過 macOS 隱私控制。
- 如果 `screencapture` 失敗或傳回隱私提示，自動化呼叫端也需要 macOS 螢幕錄製權限。

## Known Local App Facts

在此電腦上觀察到：

- App 路徑：`/Applications/GitHub Copilot.app`
- Bundle id：`com.github.githubapp`
- Bundle 版本：請使用 `map-app.sh` 動態讀取；請勿依賴儲存的版本
- 可執行檔：`/Applications/GitHub Copilot.app/Contents/MacOS/github`
- 二進位類型：原生 macOS Mach-O arm64
- 觀察到的架構包括 AppKit 與 WebKit。
- 桌面 app 顯然不是 Electron/Chromium；請勿假設可使用 Playwright/CDP 控制。

## Known Accessibility Map

在啟用 macOS Accessibility 權限後，`System Events` 可以將 app 定址為處理序 `GitHub Copilot`。

實用的公開控制項（側邊欄 1.1.32、Customize 1.1.30、其他控制項 1.1.26；完整對應請參閱 [app-ui-map.zh-Hant.md](references/app-ui-map.zh-Hant.md)）：

| 控制項 | 角色 | 用途 |
|---|---|---|
| `GitHub Copilot` | `AXWindow` | 主要 app 視窗 |
| `GitHub Copilot` | `AXWebArea` | 主要以 webview 為基礎的 app 內容 |
| `Sidebar` | `AXGroup` | 側邊欄區域 |
| `Toggle sidebar, Command + B` | `AXCheckBox` | 收合/展開側邊欄 |
| `New`、`Pull requests`、`Issues`、`Dashboards`、`Automations`、`Customize` | `AXButton` | 側邊欄檢視（快速連結） |
| `Customize sections` | `AXTabGroup` | Customize 的 **Browse** 與 **Installed** 索引標籤（1.1.30） |
| `Filter by type: <type>` | `AXPopUpButton` | Customize 類型篩選：All types、MCP、Plugins、Skills、Extensions、Canvas |
| `New repository or session` | `AXPopUpButton` | 工作階段 **+** 功能表 |
| `New session in <project>` | `AXButton` | 特定專案的新工作階段入口（在滑鼠暫留或取得焦點時可見） |
| `Message` | `AXTextArea` | 提示撰寫區 |
| `Project: <project>` | `AXComboBox` | 撰寫區專案選擇器 |
| `Workspace: <choice>, branch: <branch>` | `AXPopUpButton` | **Where to work** 功能表 |
| `Toggle panel, Command + Option + B` | `AXCheckBox` | 檢閱面板 |
| `Add tab, Command + T` | `AXPopUpButton` | 檢閱面板索引標籤功能表 |

如需指令碼與注意事項，請參閱 [macos-accessibility.zh-Hant.md](references/macos-accessibility.zh-Hant.md)。

## Preferred Workflows

### Map the App Safely

1. 確認 app 正在執行，並探索 bundle/處理序狀態。
2. 使用 app/工作階段工具列舉專案與工作階段。
3. 執行唯讀的 Accessibility 視窗/功能表探測。
4. 列舉 `AXWebArea` 的子項目。
5. 將發現儲存到工作階段成品，而非儲存庫，除非使用者要求持久性文件。

使用：

- [probe-readonly.applescript](sample_codes/macos-accessibility/probe-readonly.applescript)
- [automation-map.zh-Hant.md](references/automation-map.zh-Hant.md)

### Test Composer Control Without Submitting

1. 建立一個獨立的閒置工作階段。
2. 導覽至該工作階段。
3. 將焦點放在 `AXTextArea name=Message`。
4. 直接設定草稿值。
5. 讀回該值。
6. 以 Command+A 與 Delete 清除它。
7. 驗證傳回的 `cleared-value` 為空。

使用 [type-and-clear-draft.applescript](sample_codes/macos-accessibility/type-and-clear-draft.applescript)。

### Capture Screenshots for Course Assets

僅在使用者同意 App 可以可見，或目標狀態已顯示在專用 App 視窗中時，才使用此工作流程。請從訓練分支上的**已清理訓練帳戶**擷取 — 絕不使用使用者真實的私人專案。

1. 對所參考的影像執行 `screenshot-context.py`，並將附近的
   課程文字轉換為具體的擷取檢查清單。如果要重新擷取既有的
   影像，請先檢視舊影像，並列出其標註、方框與裁切範圍。
2. 使用 `prepare-persona.sh` 從持續存在的 `demo` persona
   啟動新的處理序。前置步驟會設定標準的 1920x1080 點視窗、
   重設縮放、放大三次，並啟用與驗證 **Streamer Mode**。若
   無法驗證 Streamer Mode 或縮放，它會以封閉方式失敗。請傳入確切的
   本機訓練儲存庫，並保留傳回的處理序 ID。擷取之前請關閉 Siri：
   其波形球體可能出現在取得焦點的文字欄位旁，
   而當擷取指令碼看到該球體時就會失敗。
3. 使用 `COPILOT_PID="$COPILOT_PID" bash "$SK/map-app.sh"` 對應該處理序，讓步驟
   符合目前的 app 組建。
4. 使用 Accessibility 控制項將該處理序導覽至確切的狀態，並讓
   載入動畫穩定下來。對於具名動作無法建立的狀態，請使用 `prepare-copilot-state.swift`：
   `focus` 會顯示僅在滑鼠暫留時出現的控制項（例如 **Chats** 列上的
   **+**），`highlight-menu-item` 會以鍵盤選取功能表項目
   （網頁功能表會忽略合成的指標移動），`set-value` 會填入
   欄位而不按 Return，而 `park` 會將指標與焦點移開，
   使暫留效果或焦點環不再殘留。
5. 使用
   `locate-copilot-element.swift` 找出每個控制項的最終座標，然後擷取該處理序
   ID 所擁有的視窗並轉換為 WebP。請依指示順序傳入標註、醒目提示
   方框與選用的裁切範圍，全部使用最終的 1920x1080 座標：
   ```bash
   bash "$SK/capture-window.sh" \
     <chapter>/assets <base-name> 40 "$COPILOT_PID"

   bash "$SK/capture-window.sh" \
     00-setup/assets app-add-project 40 "$COPILOT_PID" \
     --callout 1:372:338 --callout 2:585:437

   bash "$SK/capture-window.sh" \
     06-canvases/assets app-open-repo-issues-canvas 40 "$COPILOT_PID" \
     --box 1287:266:1551:309 --crop 352:0:1920:700
   ```
6. 對於有標註的影像，請確認每個徽章都緊鄰其控制項、不會
   遮住標籤或圖示，且符合指示順序。標準樣式
   是半徑 26px、顏色 `#ff594b` 的圓形，搭配白色數字。每個徽章
   與影像邊緣的距離至少要有其半徑加 2 px。
7. 在網頁/課程頁面中使用 WebP。請勿在章節 `assets`
   資料夾中留下 PNG 檔案：請擷取到 `/tmp` 之類的暫存資料夾，只複製 WebP，或
   在驗證 WebP 之後刪除 `.png` 與 `.raw.png` 檔案。
8. 確認完整視窗的檔案恰好是 1920x1080。裁切後的影像會保持
   其裁切矩形的大小。
9. 確認影像在其邊緣內側有 2px 的 `#cccccc` 邊框，且
   沒有任何視窗圓角顯示黑色像素。app 視窗具有透明的
   圓角。在任何 RGB 轉換或
   縮放之前，請先將影像放在白色背景上，否則圓角會變黑。若發現
   深色圓角像素，`capture-window.sh` 會停止。
10. 確認左上角沒有顯示三個 macOS 視窗按鈕，
    且側邊欄圖示位於其位置。`capture-window.sh`
    會透過 `hide-window-controls.py` 完成此事。
11. 確認輸出保留了大頭貼，以 **Copilot Dev** 取代
   個人的設定檔名稱，並對相符的個人
   儲存庫擁有者、路徑與分支名稱使用 **copilotdev**。
12. 對於 Settings 螢幕擷圖，請確認看不到 app 版本。
13. 在提交或
   發佈之前，請檢閱螢幕擷圖是否有其他私人資料。
14. 還原您為了擷取而變更的任何 persona 狀態（隱藏的
   專案、已安裝的外掛程式或 MCP 伺服器、測試自動化或工作階段），
   然後只有在擷取經過驗證之後，才使用 `cleanup-persona.sh` 關閉確切的處理序。
   請為下一張螢幕擷圖保留已登入的 persona。

若要處理課程中待處理的擷圖，請動態探索它們並一次處理一張（切勿寫死清單）：`screenshots.sh list` -> `screenshots.sh next` -> 擷取 -> `screenshots.sh embed`。請參閱 [missing-screenshots.zh-Hant.md](references/missing-screenshots.zh-Hant.md)。

使用：

- [screenshot-capture.zh-Hant.md](references/screenshot-capture.zh-Hant.md) — 完整方法、權限，以及 macOS Spaces 限制
- [missing-screenshots.zh-Hant.md](references/missing-screenshots.zh-Hant.md) — 通用的逐張擷取工作流程（擷圖是動態探索的，絕不寫死）
- [screenshots.sh](sample_codes/macos-accessibility/screenshots.sh) — 動態 `list` / `next` / `embed` 各章節的 `<!-- app-screenshot: ... -->` 預留位置
- [screenshot-context.py](sample_codes/macos-accessibility/screenshot-context.py) — 擷取定義螢幕擷圖所需狀態的課程章節
- [prepare-persona.sh](sample_codes/macos-accessibility/prepare-persona.sh) — 從已登入的 persona 啟動新的處理序，在缺少本機儲存庫時新增它，在側邊欄中檢查其資料夾名稱，並傳回處理序 ID
- [ensure-streamer-mode.swift](sample_codes/macos-accessibility/ensure-streamer-mode.swift) — 在螢幕擷圖準備之前啟用並驗證 Streamer Mode
- [cleanup-persona.sh](sample_codes/macos-accessibility/cleanup-persona.sh) — 停止確切的一個螢幕擷圖處理序，並保留已登入的 persona
- [launch-persona.sh](sample_codes/macos-accessibility/launch-persona.sh) — `prepare-persona.sh` 使用的低階啟動器
- [control-copilot-window.swift](sample_codes/macos-accessibility/control-copilot-window.swift) — 為確切的一個 app 處理序設定 1920x1080 點的擷取框架與經量測的 150% 擷取縮放（內建顯示器上為 125%）
- [control-copilot-ui.swift](sample_codes/macos-accessibility/control-copilot-ui.swift) — 在確切的 app 處理序中按下一個具名的 Accessibility 控制項
- [prepare-copilot-state.swift](sample_codes/macos-accessibility/prepare-copilot-state.swift) — 準備暫時性狀態：將焦點放在僅暫留時出現的控制項、醒目提示功能表項目、設定欄位值、關閉橫幅，以及移開指標
- [locate-copilot-element.swift](sample_codes/macos-accessibility/locate-copilot-element.swift) — 列印具名控制項的最終 1920x1080 座標，供標註、方框與裁切使用
- [find-private-identities.swift](sample_codes/macos-accessibility/find-private-identities.swift) — 在不寫死身分的情況下，衍生設定檔名稱與相符的個人儲存庫擁有者
- [sanitize-screenshot.sh](sample_codes/macos-accessibility/sanitize-screenshot.sh) — 在保留大頭貼的同時取代身分文字；由於對話方塊可能隱藏側邊欄，因此依處理序快取身分
- [app-ui-map.zh-Hant.md](references/app-ui-map.zh-Hant.md) — 已清理的事實性 UI 對應（功能表、側邊欄、檢視、撰寫區控制項），可透過 map-app.sh 重新產生
- [capture-window.sh](sample_codes/macos-accessibility/capture-window.sh) — 建議使用：依處理序篩選的 CoreGraphics 視窗擷取加上 WebP，支援 `--callout`、`--box`、`--arrow` 與 `--crop`
- [finalize-screenshot.py](sample_codes/macos-accessibility/finalize-screenshot.py) — 強制輸出為 1920x1080，加入必要的 2px `#cccccc` 內側邊框，並進行裁切
- [hide-window-controls.py](sample_codes/macos-accessibility/hide-window-controls.py) — 移除 macOS 視窗按鈕並將側邊欄圖示移到其位置，讓螢幕擷圖不顯示作業系統
- [add-step-callouts.py](sample_codes/macos-accessibility/add-step-callouts.py) — 在最終的螢幕擷圖上加入有順序的紅色步驟徽章、紅色醒目提示方框與紅色箭頭
- [find-copilot-window.swift](sample_codes/macos-accessibility/find-copilot-window.swift) — 列出畫面上的 Copilot 視窗及其視窗與處理序 ID
- [map-app.sh](sample_codes/macos-accessibility/map-app.sh) — 附有版本戳記的 UI 對應，用於為步驟提供依據以及比對 app 更新
- [capture-copilot-window.sh](sample_codes/macos-accessibility/capture-copilot-window.sh) — 較舊的 Accessibility 矩形後援方式

### Drive App Work Without GUI Scripting

優先使用工作階段協調：

- `list_projects` 用於尋找專案 id。
- `create_session` 用於建立新的隔離工作階段。
- `send_session_message` 用於交付該工作階段的工作。
- `get_session` 用於檢查路徑與中繼資料。
- 僅在使用者明確希望查看該工作階段時才使用 `navigate_to`。

請參閱 [session-orchestration.zh-Hant.md](references/session-orchestration.zh-Hant.md)。

### Use Canvases for Structured Human-Agent Work

對於測試看板、儀表板、課程追蹤器或可見狀態，請使用 canvas 介面，而非 GUI 點擊。Canvas 是雙向的，並且可以公開代理程式可呼叫的動作。請參閱 [canvas-and-automation.zh-Hant.md](references/canvas-and-automation.zh-Hant.md)。

## Decision Table

| 目標 | 最佳機制 |
|---|---|
| 了解工作階段的行為 | 建立獨立的工作階段並比較輸出 |
| 在桌面 UI 中輸入/清除提示 | macOS Accessibility `Message` 文字區域 |
| 點擊具名的 app 按鈕 | 依角色/名稱/動作使用 Accessibility |
| 擷取課程螢幕擷圖 | Accessibility 視窗邊界 + `screencapture` PNG + WebP 轉換 |
| 啟動週期性實驗 | App 自動化/工作流程 |
| 建置可見的實驗儀表板 | Canvas 擴充功能 |
| 讓 CLI 工作階段在離開電腦時仍可操控 | Copilot CLI 遠端控制 |
| 測試 PR/issue 生命週期 | App 工作階段 API 加上 GitHub issue/PR 工具 |
| 完整的原生 UI 迴歸測試 | 明確設定後使用 Appium Mac2 |

## Source References

- GitHub Copilot app 概觀：<https://docs.github.com/en/copilot/concepts/agents/github-copilot-app>
- 代理程式工作階段：<https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions>
- Canvas 擴充功能：<https://docs.github.com/en/copilot/how-tos/github-copilot-app/working-with-canvas-extensions>
- 自動化：<https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations>
- Copilot CLI 遠端控制：<https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-remote-control>
- Apple UI 指令碼：<https://developer.apple.com/library/archive/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/AutomatetheUserInterface.html>
