# macOS Accessibility Automation

## Permission Requirement

必須將 macOS Accessibility 控制權限授予自動化呼叫端。如果 `osascript` 傳回輔助使用存取錯誤，請停止並請使用者在「系統設定」中授予存取權。

典型錯誤：

```text
System Events got an error: osascript is not allowed assistive access.
```

## Read-Only Probe Pattern

請先使用唯讀探測：

```bash
COPILOT_PID="$COPILOT_PID" bash .github/skills/github-copilot-app-automation/sample_codes/macos-accessibility/map-app.sh
osascript .github/skills/github-copilot-app-automation/sample_codes/macos-accessibility/probe-readonly.applescript "$COPILOT_PID"
```

預期有用的輸出包括：

- 處理序名稱
- 視窗名稱
- 功能表列項目
- `AXWebArea` 子項目
- 具名的按鈕/群組/文字控制項

## Control Discovery Notes

Copilot App 殼層可能如下所示：

```text
AXWindow GitHub Copilot
  AXGroup
    AXGroup
      AXScrollArea
        AXWebArea GitHub Copilot
```

已知控制項：

```text
AXGroup      Sidebar
AXCheckBox   Toggle sidebar
AXPopUpButton Create new project or session
AXButton     New session in <project>
AXTextArea   Message
AXComboBox   Select model
AXGroup      Conversation timeline
AXButton     Open changes
```

## Safe Draft Prompt Test

使用草稿測試來確認對提示撰寫區的寫入權限，而不送出：

```bash
osascript .github/skills/github-copilot-app-automation/sample_codes/macos-accessibility/type-and-clear-draft.applescript "$COPILOT_PID"
```

預期的格式：

```text
direct-set-worked=true
typed-value=DRAFT ONLY - accessibility automation probe - do not submit
cleared-value=
```

## Dedicated Persona Launch

對於螢幕擷圖工作，請勿使用 `open -a`，因為它可能會選取既有的 app
執行個體。請從持續存在、已登入的 persona 啟動新的處理序，並
驗證確切的儲存庫：

```bash
SK=.github/skills/github-copilot-app-automation/sample_codes/macos-accessibility
PERSONA="demo"
# 訓練分支的本機複本，而非上游課程儲存庫。
COPILOT_PID="$(bash "$SK/prepare-persona.sh" \
  "$PERSONA" "$HOME/copilot-app-for-beginners" 60)"
```

請將傳回的處理序 ID 用於後續的 Accessibility 操作，並將其傳遞
給所提供的 AppleScript 探測與 `capture-window.sh`。App/工作階段 API
屬於代管代理程式的 Copilot 執行個體，可能會導覽到錯誤的
視窗。persona 啟動之後，請透過範圍限定於其處理序 ID 的
Accessibility 選取器來操控螢幕擷圖執行個體。

`prepare-persona.sh` 需要既有且已登入的 persona。如果儲存庫
不存在，它會開啟原生資料夾選擇器並提供確切的路徑。在所選
處理序中出現該儲存庫的資料夾名稱之前，它不會成功。
它檢查的是名稱，而非完整路徑。在擷取經過驗證之後，請使用
相同的 persona 名稱與處理序 ID 執行 `cleanup-persona.sh`。清理會保留 persona 與登入。

## Screenshot Identity Sanitization

依處理序執行的 `capture-window.sh` 會自動：

- 從所選處理序讀取設定檔控制項。
- 衍生該人員的顯示名稱，而不是使用名稱清單。
- 透過正規化
  比較，衍生相符的帳戶代號與個人儲存庫擁有者。
- 將設定檔文字取代為 `Copilot Dev`。
- 將相符的帳戶代號與儲存庫擁有者取代為 `copilotdev`，
  包括路徑與分支名稱內的文字。
- 將 macOS 帳戶名稱與電腦名稱取代為 `copilotdev` 與
  `copilot-dev-mac`。
- 保持大頭貼以及無關的人員與組織不變。
- 以 OCR 驗證來源身分文字已不存在。
- 當提供重複的
  `--callout NUMBER:X:Y` 引數時加入有順序的紅色編號標註，使用
  `--box LEFT:TOP:RIGHT:BOTTOM` 加入紅色醒目提示方框，
  使用 `--arrow TAIL_X:TAIL_Y:HEAD_X:HEAD_Y` 加入紅色箭頭，並以 `--crop` 進行帶邊框的裁切。
- 如果在取得焦點的欄位旁可見 Siri 波形球體，則會失敗。

此步驟需要 `tesseract` 與 Pillow。如果身分偵測成功，但
OCR 找不到或無法移除文字，擷取會失敗並保留 `.raw.png` 檔案
以供手動檢閱。

## Robustness Requirements

- 對於螢幕擷圖工作，請執行 `launch-persona.sh` 並以其傳回的處理序
  ID 為目標，而不是開啟或選取任意的執行個體。
- 在將焦點放在 app 之後加入短暫延遲。
- 在每次操作之前，重新尋找 `AXWebArea` 與 `Message` 文字區域。
- 可能的話，優先直接設定 `value`，而非模擬輸入。
- 如果直接設定失敗，只有在將焦點放在文字區域之後才退回使用 `keystroke`。
- 以 Command+A 然後 Delete 清除草稿。
- 除非使用者明確要求送出，否則切勿傳送 Return/Enter。
