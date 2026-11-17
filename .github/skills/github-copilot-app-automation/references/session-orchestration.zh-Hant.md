# 工作階段協調

## 請在 GUI 自動化之前使用

當目標是了解或測試 Copilot App 的行為時，請透過 app/工作階段 API 建立並驅動隔離的工作階段，而不是點選原生 UI。

## 安全的工作階段實驗流程

1. 列出專案並選擇目標專案。
2. 以具描述性的名稱建立獨立的工作階段。
3. 若進行 GUI 撰寫區測試，請讓它保持閒置。
4. 僅在使用者希望代理程式實際執行工作時，才使用 `send_session_message`。
5. 以 `get_session` 檢查該工作階段。
6. 僅在使用者明確希望 App UI 變更時，才導覽至該工作階段。

## 隱藏工作階段的需求

當使用者要求隱藏或獨立的執行個體時：

- 可行時，請使用 `create_session` 搭配 `coordinate_with_creator: false` 與啟動提示。
- 後續工作請使用 `send_session_message`。
- 請勿呼叫 `navigate_to`；它會切換使用者可見的 App 視窗。
- 請勿使用前景的 `open -a`、功能表命令、資料夾選擇器對話方塊或無障礙點選。
- 若本機資料夾尚未成為專案，請先將其設為獨立的存放庫根目錄，以免 App 解析到上層存放庫。
- 若沒有非互動式的專案註冊途徑，請停止並回報該限制，而不是讓使用者選取資料夾。
- 隱藏的工作階段可透過 `get_session`、工作階段路徑中的檔案系統檢查，以及工作階段事件中繼資料來驗證。

## 範例提示策略

請使用能清楚標示測試意圖的提示：

```text
This is a controlled Copilot App automation experiment. Do not edit files. Tell me the session mode you appear to be running in and what project context is available.
```

對於具破壞性的工作流程，請先建立可拋棄的專案/工作階段。

## 要觀察的內容

- 工作階段名稱與 id。
- 專案 id 與存放庫/資料夾路徑。
- 是否建立了 worktree。
- 模式行為：Interactive 對比 Plan 對比 Autopilot。
- App 是否公開 diff、時間軸事件、終端機輸出與撰寫區控制項。
- 是否能從另一個工作階段傳訊給該工作階段。
- 工作階段是否維持隱藏：沒有 `navigate_to`、沒有前景的 `open`、沒有功能表/選擇器互動。

## 清理

- 除非使用者明確要求，否則請勿刪除工作階段。
- 除非擁有權明確且使用者核准，否則請勿移除 worktree 或資料夾。
- 除非使用者要求存放庫文件，否則請將觀察結果記錄在工作階段成品中。
