# Canvas 與自動化

## Canvas

當聊天不夠用，且使用者與代理程式都需要共用的工作介面時，請使用 canvas。

適合 Copilot App 自動化 canvas 的點子：

- 課程研究看板。
- 工作階段實驗儀表板。
- Issue 分類看板。
- PR 檢閱檢查清單。
- App UI 操作介面地圖。
- 無障礙控制項清單。

Canvas 提示模式：

```text
/create-canvas Create a GitHub Copilot app automation research board with cards for sources, app controls, experiments, risks, and course ideas. Add actions to add a card, move a card, mark a source verified, record an experiment result, and export the board to markdown.
```

## 自動化

請將自動化用於週期性的代理程式工作，而非低階的 GUI 控制。

適合的自動化點子：

- 每日摘要未結的 PR。
- 每週檢閱過期的 issue。
- 依需求執行課程存放庫健全狀態檢查。
- 排程提示以收集 app 行為筆記。

安全性：

- 選取最低權限的工具。
- 避免包含機密的提示。
- 優先採用手動/依需求執行。
- 雲端自動化可能涉及原則、可見度與計費方面的影響。
