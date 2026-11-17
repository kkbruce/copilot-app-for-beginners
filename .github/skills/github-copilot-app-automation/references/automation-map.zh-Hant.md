# GitHub Copilot app 自動化地圖

## 代理程式可用的介面

### 工作階段與專案 API

可行時，請在 GUI 自動化之前使用這些：

| 工具系列 | 它能做到什麼 |
|---|---|
| `list_projects` | 探索專案 id、存放庫名稱、預設分支、本機路徑 |
| `list_sessions_and_chats` | 查看作用中與歷史的 app 工作階段/聊天 |
| `get_session` | 檢查工作階段的路徑、專案、分支/worktree 中繼資料 |
| `create_session` | 建立隔離的本機或雲端 app 工作階段 |
| `send_session_message` | 以提示來驅動另一個工作階段 |
| `navigate_to` | 將可見的 app UI 切換到某個工作階段 |
| `open_issue_session` / `open_pr_session` | 建立或開啟 issue/PR 專屬的工作階段 |
| `create_pull_request`, `update_pull_request` | 在 app 環境中處理 PR 生命週期 |
| `add_pr_review_comment` | 在可用時暫存 PR 檢閱意見 |

### Canvas API

當工作需要可見的結構化狀態時，請使用 canvas：

- 開啟/聚焦某個 canvas。
- 叫用 canvas 動作。
- 在可用時使用編輯器/瀏覽器/終端機/文件 canvas。
- 為專案看板、研究儀表板、分類看板、發行檢查清單與工作啟動器建立自訂 canvas 擴充功能。

### 工作流程 API

請將工作流程用於可重複的實驗：

- `list_workflows`
- `save_workflow`
- `run_workflow`

研究期間觀察到的狀態：不存在已儲存的工作流程。

### macOS 無障礙

當必須操作可見的 app UI 時，請使用無障礙功能：

- 處理序：`GitHub Copilot`
- 視窗：`GitHub Copilot`
- 主要內容：描述為 `GitHub Copilot` 的 `AXWebArea`
- 撰寫區：`AXTextArea name=Message`
- 模型選擇器：`AXComboBox name=Select model`

## 建議的自動化階梯

1. **使用 app/工作階段 API** 以進行可靠的協調。
2. **使用 canvas** 處理可見的共用狀態與可供代理程式呼叫的功能。
3. **使用工作流程/自動化** 處理可重複的工作。
4. **僅在 GUI 專屬行為時** 才使用無障礙功能。
5. **僅當** 無障礙指令碼需要成為耐用的回歸測試時，才使用 Appium Mac2。

## 反面模式

- 除非明確獲准，否則請勿依螢幕座標點選。
- 未經使用者明確核准，請勿從無障礙指令碼提交提示或執行具破壞性的動作。
- 請勿假設該 app 是 Electron。
- 請勿假設視窗會持續處於公開狀態；請一律重新探索處理序/視窗/網頁區域。
- 請勿將私人內容的大範圍 UI 擷取自動化寫入存放庫檔案。
