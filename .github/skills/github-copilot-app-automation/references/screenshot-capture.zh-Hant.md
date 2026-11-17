# Screenshot Capture and WebP Optimization

## 目標

擷取課程教材所需的 GitHub Copilot app 可見狀態：

- 儲存 **PNG** 作為高品質來源成品。
- 儲存 **WebP** 最佳化版本供網頁傳遞。
- 讓整個視窗的檔案精確為 **1920x1080**。裁剪後保持其
  裁剪矩形的大小。
- 在影像邊緣內側加上 **2px `#cccccc` 邊框**，裁剪後的影像也一樣。
- 從左上角移除 macOS 視窗按鈕（關閉、最小化、縮放），並將側邊欄圖示
  移到其位置，讓螢幕擷取畫面
  不會顯示作業系統。`capture-window.sh` 會為此執行
  `hide-window-controls.py`。
- 不要讓視窗圓角變成黑色。視窗具有透明的
  圓角。在進行任何 RGB 轉換或調整大小之前，請先將影像置於白色背景上。
  `capture-window.sh` 會在加入註解之前檢查圓角，並在
  發現深色像素時停止。
- 擷取標準的 1920x1080 點視窗，而非全螢幕。
- 重設顯示縮放，然後精確放大三次（150%），再準備
  畫面。
- 在準備畫面之前，先啟用並驗證 **Streamer Mode**。
- 擷取後，從 Settings 螢幕擷取畫面中移除應用程式版本。
- 當一張影像顯示多個讀者必須依序選取的控制項時，加入有順序的紅色數字標註。
  單一項目請使用紅色強調方塊。
- 使用已知且已可見的獨立工作階段，或在變更可見的 App 視窗之前取得明確核准，讓螢幕擷取畫面保持可預測且安全。

## 專用 Persona 執行個體

針對每張課程螢幕擷取畫面，從持久且已登入的
`demo` persona 啟動新的處理程序，並驗證確切的本機訓練存放庫：

```bash
SK=.github/skills/github-copilot-app-automation/sample_codes/macos-accessibility
PERSONA="demo"
# The local clone of the training fork, not the upstream course repository.
REPO="$HOME/copilot-app-for-beginners"
COPILOT_PID="$(bash "$SK/prepare-persona.sh" "$PERSONA" "$REPO" 60)"
```

準備工作流程：

- 需要現有且已登入的 persona 首頁。
- 將登入和帳戶狀態保留在 `CopilotPersonas/demo` 中。
- 使用 `open -na` 啟動新的應用程式執行個體。
- 尋找新的處理程序，而不是重複使用現有的 Copilot 處理程序。
- 只將該處理程序的主視窗設為標準的 1920x1080 點框架，
  並在其顯示器上置中。
- 重設顯示縮放，放大三次，並測量側邊欄控制項以
  確認 150% 縮放。使用 `COPILOT_CAPTURE_DISPLAY=builtin` 時，它會使用
  內建的筆記型電腦顯示器和兩個縮放步驟（125%）。若無法確認縮放，它會重試並失敗。
- 啟用並驗證 Streamer Mode，以隱藏未發行的功能。
- 驗證 persona 已登入。
- 如果側邊欄中沒有具有該資料夾名稱的
  存放庫，則透過原生資料夾選擇器開啟確切的存放庫路徑。
- 驗證存放庫的資料夾名稱出現在所選的處理程序中。
  它檢查的是名稱，而非完整路徑。
- 關閉暫時性橫幅（僅限安全允許清單上的按鈕），並將
  指標移離內容。
- 列印其處理程序 ID，以便精確指定擷取目標。

獨立的 `HOME` 會隔離應用程式檔案，但不會隔離
macOS Keychain 中的認證。新的執行個體仍可能顯示已登入的帳戶名稱
和頭像。特定處理程序的擷取會讀取設定檔控制項，將
該人員的名稱取代為 **Copilot Dev**，並保留頭像。當可見的帳戶控點或存放庫擁有者
與正規化的設定檔名稱相符時，也會將其取代為 **copilotdev**，包括路徑和分支名稱中的值。
終端機提示和路徑可能顯示的
macOS 帳戶名稱和電腦名稱，會變成
**copilotdev** 和 **copilot-dev-mac**。它不會寫死真實名稱，也不會取代
無關的人員或組織。在 Settings 畫面上，它會移除顯示的應用程式版本，
因為該值經常變動。

這些身分識別會在側邊欄第一次可見時，針對每個處理程序加以快取，
因為開啟的對話方塊可能使 Accessibility 看不到側邊欄。
`cleanup-persona.sh` 會移除該快取。

自動化呼叫端需要 macOS Accessibility 權限，才能設定視窗
框架和縮放。課程擷取請勿使用全螢幕：當應用程式
處於使用中時，macOS 可能會在應用程式內容上繪製功能表列和標題列。

請勿使用 `navigate_to` 或其他應用程式/工作階段 API 來準備此視窗。
這些 API 屬於代管代理程式的 Copilot 執行個體，可能會導覽
到錯誤的執行個體。請使用從傳回的處理程序 ID
所選取的 Accessibility 控制項。

成功且驗證過擷取後，只關閉暫時處理程序：

```bash
bash "$SK/cleanup-persona.sh" "$PERSONA" "$COPILOT_PID"
```

清理指令碼只會停止所提供的 GitHub Copilot 處理程序。它會保留
persona 首頁、帳戶登入和已驗證的專案設定。

## 以課程內容為擷取依據

在開啟控制項之前，先擷取影像周圍的 Markdown 區段：

```bash
python3 "$SK/screenshot-context.py" \
  01-tour-the-app/README.md \
  assets/app-quick-chat.webp
```

將該輸出作為擷取規格。檢查：

- 區段標題指出了正確的應用程式介面。
- 影像之前的步驟指出了必須已選取的控制項。
- 影像之後的步驟指出了必須保持可見的功能表或欄位。
- 目前的 Accessibility 標籤與課程中的文字相符。
- 若使用數字標註，則依循附近指示中的順序。

只有在以下所有條件皆成立時才使用標註：

- 一張螢幕擷取畫面顯示兩個或更多讀者必須選取的控制項。
- 這些控制項在擷取的狀態中同時可見。
- 附近的文字提供相同的動作順序，並為每個標註命名。

請勿在單一動作的螢幕擷取畫面、輸出範例、結果
表格、終端機證據，或僅用於說明概念的影像上加入標註。

如果目前的標籤與課程不同，請更新直接相關的文字。
不要為了讓舊文字保持不變而重現過時的 UI。

重拍現有影像時，先檢視舊影像。在新位置的相同控制項上重新建立其
數字標註、強調方塊和箭頭，
並保持相近的裁剪。如果舊目標已不存在（例如
**Running...** 狀態現在幾秒內就結束），請指向
顯示相同結果的最接近元素，並在您的報告中說明。

## 準備暫時性 UI 狀態

許多課程狀態很難只靠一般按鈕按下來建立。請使用
[prepare-copilot-state.swift](../sample_codes/macos-accessibility/prepare-copilot-state.swift)
和 [locate-copilot-element.swift](../sample_codes/macos-accessibility/locate-copilot-element.swift)：

| Need | Approach |
|---|---|
| 顯示僅在滑鼠停留時出現的控制項，例如 **Chats** 列上的 **New chat** (+) 或專案列上的 **Create from** | `focus <label>`：取得焦點的控制項也會顯示其焦點環和工具提示 |
| 將功能表項目顯示為已選取 | 開啟功能表，然後 `highlight-menu-item <label>`。Web 功能表會忽略合成的指標移動，因此這會按下 Down，直到該項目成為使用中 |
| 填入欄位但不提交 | `set-value <label> <text>` 絕不會按下 Return |
| 移除停留效果、工具提示和焦點環 | `park`（當影像必須顯示取得焦點的欄位時，加入 `keep-focus`） |
| 移除促銷和通知 | `dismiss-banners` 只會按下允許清單中的按鈕，例如 **Close banner** 或 **Not now** |
| 放置標註、方塊和裁剪 | `locate-copilot-element <pid> <label> [role]` 會列印最終的 1920x1080 座標 |

其他觀察到的特殊行為：

- 開啟的對話方塊會使 Accessibility 看不到側邊欄。清理程序會使用
  先前對同一處理程序擷取時的身分識別快取。
- 以鍵盤調整大小後，檢閱面板分隔線會保持焦點。擷取之前，請按 Tab，
  然後按 Escape。
- 首次執行的學習者沒有專案，因此 Projects **+** 選單不會顯示
  **Start session in** 群組。若要顯示其檢視，請清除 **Show in sidebar**（位於
  專案的 Settings 頁面），擷取，然後再次選取。
- 擷取後還原 persona 的每項變更：隱藏的專案、已安裝的
  外掛程式和 MCP 伺服器、測試自動化，以及暫時工作階段。
- 檢閱面板不會在 New 檢視中開啟。請提交第一個提示，使
  工作階段存在，然後再開啟面板。
- 每次擷取都會將 persona 視窗帶到最前面。使用者此時在另一個應用程式中
  輸入的 Return 按鍵會傳到 persona。當
  **Review plan** 或 **Question** 方塊正在等待時，Return 會提交所選的
  選項，而 **Approve and implement this plan** 會啟動 Autopilot。請快速擷取
  這些狀態，然後選擇 **Exit plan mode and I will prompt myself**
  或刪除工作階段。如果代理程式誤啟動了工作，請停止它，檢查
  worktree，並刪除工作階段。
- 若要在新工作階段顯示分支名稱 `copilotdev-…`，請將 **Settings** >
  **Sessions** > **Default branch prefix** 設為 `copilotdev-`，然後再開始
  工作階段，並在擷取後還原 `%username%-`。清理程式會取代
  大多數文字中的名稱，但淡色或部分隱藏的分支文字可能
  仍可讀取。
- `/` 和 `@` 預先輸入列中的長描述會在約
  半秒後橫向捲動。若要顯示文字開頭，請輸入命令並在
  約 0.3 秒內擷取，或使用 `screencapture -l <window id>` 擷取，
  然後自行執行清理程式和完成程式。
- Terminal 提示會顯示 macOS 使用者和電腦名稱。擷取之前，
  請在 Terminal 索引標籤中執行 `PROMPT='%1~ %# '` 和 `clear`，讓提示只顯示
  資料夾名稱。
- 新的自訂代理程式檔案只會在應用程式重新啟動後載入。使用
  `cleanup-persona.sh` 關閉 persona，然後以 `prepare-persona.sh` 再次啟動。
- Persona 的工作區選擇會持續存在。在變更之前，請記下工作區選擇器的值
  （**New worktree** 或 **Current checkout**），並在擷取後
  還原。
- 在登入和導覽畫面上，`launch-persona.sh` 無法設定
  縮放，也不會列印處理程序 ID。請依其 `HOME` 值尋找 persona 處理程序。
- 網站安裝按鈕會開啟 `ghapp://` 連結。請勿以 `open` 開啟該
  連結，因為 macOS 會將它傳送給預設的應用程式執行個體，而那可能是
  使用者自己的應用程式。只能將它傳送給 persona 的處理程序 ID。代理程式的主機
  應用程式無法取得 macOS Automation 權限，因此請使用小型已簽署輔助
  應用程式，並宣告 `NSAppleEventsUsageDescription`。macOS 隨後會要求使用者
  **Allow** 一次。
- 所有應用程式執行個體都使用實際
  `~/Library/WebKit/com.github.githubapp` 資料夾中相同的 WebKit 儲存體。Persona 的活動可能會變更
  使用者自己應用程式中的某些 Web UI 狀態，例如
  Issues 和 Pull requests 檢視中的存放庫選取項目。
- `tesseract` 無法讀取 `/tmp` 中的檔案，因為它會將 `/tmp` 解析為
  `/private/tmp`，然後失敗。它會列印錯誤，而對其
  輸出執行 grep 的掃描會回報沒有符合項目。請改為以管線傳入影像
  （`dwebp -quiet a.webp -o - | tesseract stdin -`），或將檔案保留在
  工作階段資料夾中。請將空的 OCR 輸出視為失敗，而非乾淨的結果。
- 預先輸入列、專案選擇器列和某些選項按鈕會回報錯誤的
  Accessibility 框架（例如 955x911）。此類框架的中心可能
  位於 persona 視窗之外，而在該處的實際點按會傳到另一個應用程式。
  請改為點按可見的列，並拒絕 persona
  視窗框架之外的任何點。
- `/chronicle standup` 會回報已登入帳戶最近一天的
  所有工作階段，包括來自其他電腦的工作階段。請捲動聊天，使只顯示
  課程工作階段，並針對其他工作階段的名稱執行 OCR 隱私
  檢查。
- 清理程式有時無法以 OCR 找到設定檔名稱。此時它只會保留
  `<name>.raw.png`。請立即刪除該原始檔案，並重新擷取。
- 當應用程式暫存更新時，右下角會出現 **Ready to update** 快顯通知（Restart now、
  Later），檔案位於
  `<persona>/.copilot/updater`。請選取 **Later**。關閉 persona 之前，
  請刪除 `staged-update.bin` 和 `staged-manifest.json`，使 persona 無法
  將更新安裝到共用的 `/Applications` 應用程式套件中。
- 第 06 章 Feature Workbench 時間（Claude Opus 5.5）：`/create-canvas`
  約需 5 分鐘。**Generate plan** 只會顯示 **Working...** 約
  15 秒，或在代理程式重複使用先前的計畫時約 3 秒，因此請在
  全新的工作階段中於 2 秒內擷取。**Run baseline** 約在 6 秒後結束，
  因此請在測試工具呼叫展開的情況下擷取完成狀態。
  **Browser validation** 可能會將開發伺服器作為已附加的背景
  工作啟動。此時回合會保持開啟，而動作會停留在 **Working...**（20 分鐘後
  會顯示 **Timed out**），直到您在
  **Background** 中停止伺服器。代理程式也可能在 `/tmp` 中安裝 `playwright-core`。
  請檢查它是否刪除了該資料夾。

## 隱藏工作階段的限制

macOS 螢幕擷取需要來自可見顯示器/視窗的像素。除非 App 提供未來的轉譯/匯出 API，否則無法使用 `screencapture` 擷取隱藏、僅背景、最小化或僅 API 的 Copilot App 工作階段。

對於隱藏工作階段的工作：

- 不要只為了擷取螢幕畫面而呼叫 `navigate_to`。
- 未經明確核准，不要將使用者目前的 App 視窗帶到前景。
- 使用 `get_session`、工作階段事件和檔案系統狀態來驗證隱藏的工作。
- 當要求的螢幕擷取畫面與隱藏執行相衝突時，請直接說明此限制。

## 必要的 macOS 權限

可能適用兩道 macOS 隱私權關卡：

- **Accessibility**：用於找出 GitHub Copilot 視窗並讀取其位置/大小。
- **Screen Recording**：`screencapture` 可能需要它來擷取應用程式內容。

如果擷取為空白、被拒絕、傳回 `could not create image from display`，或提示使用者，請停止並請使用者授予自動化呼叫端權限。不要繞過 macOS 隱私權控制。

## 本機工具鏈

在此機器上觀察到的可用工具：

```text
screencapture=/usr/sbin/screencapture
osascript=/usr/bin/osascript
sips=/usr/bin/sips
cwebp=/opt/homebrew/bin/cwebp
magick=/opt/homebrew/bin/magick
ffmpeg=/opt/homebrew/bin/ffmpeg
pngquant=/opt/homebrew/bin/pngquant
```

偏好的轉換順序：

1. `cwebp -lossless -q 82 -m 6 -metadata none source.png -o output.webp`
2. `magick source.png -strip -define webp:lossless=true -quality 82 output.webp`
3. `ffmpeg -i source.png -c:v libwebp -lossless 1 -q:v 82 output.webp`

`sips` 適用於讀取尺寸，但在本機探測中並未顯示支援 WebP 轉換。

## 擷取策略

使用 Accessibility 取得視窗位置和大小：

```applescript
tell application "System Events"
  tell process "GitHub Copilot"
    set frontmost to true
    set windowPosition to position of window 1
    set windowSize to size of window 1
  end tell
end tell
```

然後呼叫：

```bash
screencapture -x -R "x,y,width,height" output.png
```

注意事項：

- `-x` 會停用螢幕擷取音效。
- 使用矩形擷取，是因為 Accessibility 能可靠地提供邊界；以確切的視窗 ID 擷取則需要較低層級的 CGWindow 查詢。
- 在多顯示器或 Retina 設定上，請驗證第一次擷取。如果裁剪有偏移，請擷取一次完整螢幕以判斷座標行為。

## 檔案命名

使用具描述性且可排序的名稱：

```text
assets/screenshots/01-tour-the-app-quick-chat.png
assets/screenshots/01-tour-the-app-quick-chat.webp
```

研究成品：

```text
~/.copilot/session-state/<session>/files/screenshots/<timestamp>-copilot-app.png
~/.copilot/session-state/<session>/files/screenshots/<timestamp>-copilot-app.webp
```

## 建議的工作流程

1. 使用 `screenshot-context.py` 擷取影像脈絡。
2. 從已登入的 `demo` persona 啟動新的處理程序，
   使用 `prepare-persona.sh`。
3. 確認前置步驟已設定擷取框架、確認 150% 縮放，
   並驗證 Streamer Mode 已啟用。然後使用特定處理程序的 Accessibility
   到達所需的狀態。
4. 等待 1-2 秒，讓 UI 穩定。
5. 使用以下處理程序 ID 執行 `capture-window.sh`，該 ID 由
   `prepare-persona.sh` 傳回。對於多動作螢幕擷取畫面，請加入重複的
   `--callout NUMBER:X:Y` 引數。對於單一項目，請加入 `--box
   LEFT:TOP:RIGHT:BOTTOM`。若只保留細節區域，請加入 `--crop
   LEFT:TOP:RIGHT:BOTTOM`。所有座標都使用最終的 1920x1080 影像：

   ```bash
   bash "$SK/capture-window.sh" 00-setup/assets app-add-project 40 \
     "$COPILOT_PID" \
     --callout 1:372:338 \
     --callout 2:585:437
   ```

6. 確認標註數字與附近的指示相符。將每個
   圓圈放在其控制項旁邊，且不要遮住控制項標籤或圖示。
   標準樣式是半徑 26px 的 `#ff594b` 圓圈，搭配白色數字。
7. 確認設定檔文字顯示 **Copilot Dev**、頭像保留，且任何
   相符的個人存放庫擁有者、路徑或分支名稱都顯示
   **copilotdev**。放大檢查每個被取代的範圍。
8. 確認 PNG 和 WebP 都精確為 1920x1080，或為裁剪大小。
9. 確認兩個檔案都有 2px `#cccccc` 內側邊框。
10. 對於 Settings 畫面，確認不再有可見的應用程式版本。
11. 檢查檔案大小和影像尺寸。
12. 在將影像移入課程資產之前，檢閱是否有其他私人資料。
13. 還原 persona 的變更，然後在驗證後執行 `cleanup-persona.sh`。

## 範例

```bash
SK=.github/skills/github-copilot-app-automation/sample_codes/macos-accessibility
PERSONA="demo"
COPILOT_PID="$(bash "$SK/prepare-persona.sh" \
  "$PERSONA" "$HOME/copilot-app-for-beginners" 60)"

# Navigate this new instance to the required state, then capture it.
bash "$SK/capture-window.sh" assets/screenshots 01-tour-the-app-session-ui 40 "$COPILOT_PID"
bash "$SK/cleanup-persona.sh" "$PERSONA" "$COPILOT_PID"
```

這會建立：

```text
assets/screenshots/01-tour-the-app-session-ui.png
assets/screenshots/01-tour-the-app-session-ui.webp
```

## 建議：依視窗 ID 擷取（穩健）

上述 Accessibility 矩形方法在這個以 WebKit 為基礎的應用程式上，當
`System Events` 回報 `count of windows = 0`，即使視窗可見，也可能失敗
（在應用程式 v1.0.4 中觀察到）。更可靠的方法是依 CoreGraphics
視窗 ID 擷取，並輪詢直到視窗出現。提供處理程序 ID 時，它
只會考慮該處理程序所擁有的視窗：

```bash
bash "$SK/capture-window.sh" \
  <chapter>/assets <base-name> 40 "$COPILOT_PID"
```

它使用 [find-copilot-window.swift](../sample_codes/macos-accessibility/find-copilot-window.swift)
（CoreGraphics）來找出視窗 ID。對於特定處理程序的擷取，它會
在輪詢之前立即啟用該處理程序，等待暫時性系統指示器淡出
（`COPILOT_CAPTURE_SETTLE_SECONDS`，預設為 2），並在
Siri 波形球體可見時以結束代碼 5 失敗。接著它會執行
`screencapture -x -o -l <id>`（不含視窗陰影）加上 WebP 編碼，並
在擷取看起來是空白時發出警告（這表示 Screen Recording 被拒絕）。

提供處理程序 ID 時，`capture-window.sh` 也會：

1. 從確切的應用程式處理程序讀取設定檔控制項。
2. 在不使用寫死名稱清單的情況下，推導出該人員的顯示名稱。
3. 找出其正規化值與該顯示名稱相符的存放庫擁有者。
4. 使用 OCR（3 倍灰階複本），將顯示名稱取代為 **Copilot
   Dev**、將相符的擁有者取代為 **copilotdev**，並將本機帳戶名稱和
   電腦名稱取代為 **copilotdev** 和 **copilot-dev-mac**。它會以測量到的字型、大小、粗細和色彩
   重新繪製文字，並移動該行其餘部分，讓間距保持
   不變。
5. 以另外兩次 OCR 檢查，驗證原始身分識別文字已消失。
6. 當螢幕擷取畫面顯示 Settings 時，移除顯示的應用程式版本。
7. 將已清理的 PNG 調整為精確的 1920x1080，並加入必要的 2px
   `#cccccc` 內側邊框。

提供 `--callout`、`--box` 或 `--arrow` 引數時，指令碼會在清理和
1920x1080 完成處理之後加入徽章、外框和箭頭。
標註數字必須從 1 開始連續，且只有一個標註時會被拒絕。
方塊是 4px 的 `#ff594b` 外框，不需要標註。箭頭是 5px
`#ff594b` 線條，在 `HEAD_X:HEAD_Y` 處有實心箭頭，長度至少 44px。
提供 `--crop` 時，指令碼接著會剪出該矩形，並再次加入
邊框。最後，它會將 PNG 編碼為 WebP，並驗證兩個檔案的大小和
邊框。

若要在不重拍的情況下試驗標註位置，請先不加標註擷取一次。然後
複製 PNG，在複本上以 1920x1080 座標執行 `add-step-callouts.py`，
再執行 `finalize-screenshot.py <png> --crop L:T:R:B`，並以
`cwebp -lossless -q 82 -m 6 -metadata none` 編碼。

即使未提供處理程序 ID，指令碼仍會移除 Settings 版本並強制 1920x1080。
身分識別探索需要處理程序 ID，因此課程
擷取仍必須傳入確切的 persona 處理程序 ID。

清理程式只會重新繪製身分識別文字範圍，因此設定檔頭像
仍然可見。後續步驟會變更其他像素：`hide-window-controls.py`
會移除視窗按鈕，而 `--callout`、`--box` 和 `--arrow` 會繪製在
影像上。

如果 OCR 無法定位偵測到的設定檔名稱，擷取會以失敗封閉方式處理，並保留
原始 PNG 以供手動檢閱。

## macOS Spaces 限制（重要）

CoreGraphics 和 `screencapture` 只看得到**目前使用中的
macOS Space** 上的視窗。如果 Copilot app 位於不同的桌面/Space，或位於
擷取環境無法連到的顯示器上，就找不到視窗，也無法
擷取任何內容，即使該視窗已「開啟」。請將應用程式拖曳到與執行擷取的終端機
相同的桌面，然後重試。`capture-window.sh` 會在其逾時期間
持續輪詢，因此您可以在它等待時移動視窗。

使用 `COPILOT_CAPTURE_DISPLAY=builtin` 時，persona 視窗會在內建的
筆記型電腦顯示器上開啟。每個顯示器的使用中 Space 都是可見的，因此
當您在其他顯示器上工作時，擷取仍可運作。請為 `capture-window.sh`
設定相同的變數，讓它不會啟用 persona。

## 使用已清理的訓練帳戶

實際擷取會暴露即時的專案名稱、工作階段標題、提示、差異，
以及可能的權杖。請從連線到
訓練分支的已清理訓練帳戶擷取（已執行 `node .github/scripts/setup-training-scenarios.js --yes`），
並在提交之前檢閱每張影像。完整的擷取清單，以及
哪些狀態需要種子資料，請參閱
[missing-screenshots.zh-Hant.md](missing-screenshots.zh-Hant.md)。

## 先對應

啟動 persona 之後，請使用其處理程序 ID 執行
[map-app.sh](../sample_codes/macos-accessibility/map-app.sh)，
在撰寫擷取步驟之前記錄實際的功能表和具名控制項：

```bash
COPILOT_PID="$COPILOT_PID" bash "$SK/map-app.sh"
```

應用程式更新後請重新執行，以比對確切變更的內容。
