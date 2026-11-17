![GitHub Copilot app for Beginners](assets/github-copilot-app-for-beginners.webp)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)&ensp;
[![GitHub Copilot app documentation](https://img.shields.io/badge/GitHub-Copilot_App_Docs-00a3ee?style=flat-square&logo=github)](https://docs.github.com/en/copilot/concepts/agents/github-copilot-app)&ensp;
[![Course level](https://img.shields.io/badge/Level-Beginner-success?style=flat-square)](#target-audience)

🎯 [您將學到什麼](#what-youll-learn) &ensp; 👥 [目標讀者](#target-audience) &ensp; 🤖 [Copilot 家族](#understanding-the-github-copilot-family) &ensp; 📚 [課程結構](#course-structure)

# GitHub Copilot app for Beginners

> 學習從單一桌面應用程式中指揮並協調 AI 程式設計代理程式。

GitHub Copilot app 是用於與程式設計代理程式合作的桌面控制中心，讓您在同一個地方指揮、觀察並驗證它們的工作。您不必只是向 AI 要求程式碼，而是可以給予代理程式一個目標、提供正確的脈絡，讓它們規劃並採取行動，同時由您掌控整個工作。

此應用程式將工作階段、計畫、程式碼變更、測試、瀏覽器預覽、AI 聊天、問題、pull request 等內容整合在同一處，讓您更容易指揮代理式工作並審查結果，而不必不斷在各種工具之間切換。

在整個課程中，您將學會把 Copilot app 視為引導、審查與驗證工作的地方，而不是神奇的程式碼按鈕。您將練習提供有效的脈絡、選擇正確的工作階段模式、審查證據，並判斷何時適合使用自動化，以及何時應該更密切地參與。

![GitHub Copilot app](assets/app-github-copilot-app.webp)

<a id="what-youll-learn"></a>

## 🎯 您將學到什麼

完成本課程後，您將能夠：

- 安裝、登入並設定 GitHub Copilot app
- 從提示、分支與 pull request 啟動工作階段，並附加問題作為脈絡
- 說明 Interactive、Plan 與 Autopilot 模式
- 使用以 worktree 為基礎的工作階段，而不與您的主要分支發生衝突
- 附加並管理代理程式脈絡
- 審查 diff、執行測試、預覽網頁應用程式並驗證變更
- 使用 **Issues** 與 **Pull requests** 檢視來處理問題、PR、審查留言與失敗的檢查
- 瞭解設定、指引、技能、自訂代理程式、MCP 伺服器、外掛程式、canvas 與自動化各自適用的位置

整個課程使用的主要範例位於：

```text
samples/book-app-web
```

## 目標讀者

本課程是為下列對象所設計：

- 想要協調、引導並審查由代理程式驅動的程式設計工作的開發人員
- 想要有引導式學習路徑的學生與自學者
- 評估如何在代理程式承擔更多工作的同時，讓人類保持掌控的團隊
- 想瞭解桌面應用程式定位的 Copilot CLI 或 IDE Copilot 使用者

不需要具備代理式開發的經驗。具備基本的 GitHub、Git 與軟體開發知識會有所幫助。範例應用程式是一個小型的 React/Vite 專案，因此在開發章節中，熟悉基本的 npm 命令會有幫助。請為 `samples/book-app-web` 使用目前的 [Node.js LTS](https://nodejs.org)。

GitHub Copilot app 可搭配 Copilot 方案或您自己的模型提供者使用。

<a id="understanding-the-github-copilot-family"></a>

## 🤖 瞭解 GitHub Copilot 家族

| 產品 | 執行位置 | 最適合 |
|---|---|---|
| GitHub Copilot app（本課程） | 桌面應用程式 | 監督多代理程式工作階段、計畫、diff、瀏覽器驗證、PR、canvas 與自動化 |
| IDE 中的 GitHub Copilot | VS Code、Visual Studio、JetBrains 與其他編輯器 | 代理程式、行內建議、聊天，以及以編輯器為中心的程式設計 |
| GitHub Copilot CLI | 終端機 | 終端機原生的代理程式工作與命令列工作流程 |
| Copilot cloud agent | GitHub 代管的環境 | 啟用時，在問題與雲端工作階段上進行背景工作 |

![GitHub Copilot app 在各個 Copilot 介面中的定位](assets/copilot-family-comparison.webp)

本課程聚焦於 GitHub Copilot app。在過程中，您會看到它如何連接到 GitHub、本機工具、瀏覽器預覽、終端機輸出，以及（若可用）雲端功能。

<a id="course-structure"></a>

## 📚 課程結構

| 章節 | 標題 | 學習者要做什麼 |
|:--:|---|---|
| 00 | [設定](./00-setup/README.zh-Hant.md) | 準備課程環境 |
| 01 | [導覽應用程式](./01-tour-the-app/README.zh-Hant.md) | 瞭解為何要使用此應用程式，然後導覽聊天、專案工作階段、模式、模型與設定 |
| 02 | [工作階段、Worktree 與脈絡](./02-sessions-worktrees-context/README.zh-Hant.md) | 啟動隔離的 worktree 工作階段，並使用 `@`、`#` 與 `/` 來提供脈絡與命令 |
| 03 | [開發與 GitHub 工作流程](./03-development-workflows/README.zh-Hant.md) | 審查、偵錯、測試並預覽變更，然後透過問題、PR、審查留言與檢查推進它 |
| 04 | [技能與自訂代理程式](./04-skills-custom-agents/README.zh-Hant.md) | 更新審查技能、建立唯讀的自訂代理程式，並驗證一項由技能引導的改進 |
| 05 | [MCP 伺服器與外掛程式](./05-mcp-plugins/README.zh-Hant.md) | 透過 MCP 伺服器擷取文件，並使用外掛程式的技能取得聚焦的建議 |
| 06 | [Canvas](./06-canvases/README.zh-Hant.md) | 試用社群 canvas，然後使用 `/create-canvas` 建立看板，讓計畫、進度與驗證證據保持可見 |
| 07 | [自動化](./07-automations/README.zh-Hant.md) | 建立手動的 PR 審查就緒報告、排程它，然後瞭解事件觸發程序與雲端自動化 |

## 📖 本課程如何運作

設定章節之後，每個章節都遵循相同且對初學者友善的模式：

1. 簡介：為什麼這個主題很重要
2. 錄音室類比（「From the Studio」）
3. 核心的代理程式開發概念
4. 使用 `samples/book-app-web` 的實作練習
5. 重點整理、作業（從第 02 章開始）與來源參考

> [!NOTE]
> 當章節顯示模型回應時，請記住，由於 LLM 的非確定性本質，模型輸出會有所不同。您的應用程式版本、模型、推理設定、存放庫脈絡與已啟用的工具，也可能改變回應的結構。

## 參考

- [GitHub Copilot app 概觀][about-app]
- [GitHub Copilot app 影片][about-app-videos]
- [開始使用此應用程式][getting-started]
- [使用工作階段][agent-sessions]
- [問題與 pull request][issues-prs]
- [使用自動化][automations]
- [使用 canvas 擴充功能][canvas-docs]
- [自訂 GitHub Copilot app][customizing]
- [公開的應用程式存放庫][app-readme]
- [GitHub Copilot app 變更記錄][ga-changelog]

## 附錄

- [詞彙表](./GLOSSARY.zh-Hant.md)
- [Git worktree](./appendices/git-worktrees.zh-Hant.md)
- [GitHub 訓練情境](./appendices/training-github-scenarios.zh-Hant.md)
- [疑難排解參考](./appendices/troubleshooting-reference.zh-Hant.md)
- [Book App Web 範例](./samples/book-app-web/README.zh-Hant.md)

## 授權

本專案依 MIT 開放原始碼授權條款授權。完整條款請參閱 [LICENSE](./LICENSE) 檔案。

[about-app]: https://docs.github.com/copilot/concepts/agents/github-copilot-app
[about-app-videos]: https://www.youtube.com/watch?v=LsA4vIX_3UY&list=PLNBWjViYXaIY
[getting-started]: https://docs.github.com/copilot/how-tos/github-copilot-app/getting-started
[agent-sessions]: https://docs.github.com/copilot/how-tos/github-copilot-app/agent-sessions
[issues-prs]: https://docs.github.com/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests
[automations]: https://docs.github.com/copilot/how-tos/github-copilot-app/using-automations
[canvas-docs]: https://docs.github.com/copilot/how-tos/github-copilot-app/working-with-canvas-extensions
[customizing]: https://docs.github.com/copilot/how-tos/github-copilot-app/customize-github-copilot-app
[app-readme]: https://github.com/github/app
[ga-changelog]: https://github.com/github/app/blob/main/changelog.md
