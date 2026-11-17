![第 00 章：設定](assets/chapter-header.svg)

> **現在花一點時間設定，之後就能有更多實作學習。**

在 GitHub Copilot app 中指揮代理程式之前，您需要先安裝並登入應用程式、連結您的課程存放庫副本，並準備好後續章節會用到的練習項目。本章將引導您完成設定流程，並檢查練習中會用到的工具。

## 學習目標

完成本章後，您將能夠：

- 安裝並設定 GitHub Copilot app
- 將您的課程存放庫 fork 連結為專案，並啟動專案工作階段
- 在應用程式的 **Terminal** 索引標籤中檢查必要的工具
- 執行設定指令碼，建立課程的標籤、練習議題、分支與提取要求

> ⏱️ **預估時間**：約 20 分鐘

## 先決條件

- 一個 [GitHub 帳戶](https://github.com/signup)
- 一個 [Copilot 方案](https://github.com/features/copilot/plans)，或者您可以選擇在登入時繼續使用自己的模型提供者
    - 若為 Copilot Business 或 Enterprise，必須啟用 **GitHub Copilot app** 原則。
- 在您的電腦上安裝 [Git](https://git-scm.com/install)
- [Node.js](https://nodejs.org/en/download)（標示為 **LTS** 的版本），用於執行 `samples/book-app-web` 以進行實作練習
- [GitHub CLI (`gh`)](https://cli.github.com)，用於課程中初次一次性的設定指令碼

連結存放庫後，您將在 Copilot app 的 **Terminal** 索引標籤中檢查工具版本。

## 課程主題：在錄音室中工作

在整個課程中，錄音室是貫穿全程、用來類比使用 GitHub Copilot app 的比喻。每一章都會將代理程式驅動開發的某個環節，對應到準備、錄製、檢視與精修一首曲目。

在錄製任何內容之前，您要先把錄音室準備好。您登入以取得存取權、接上設備、載入要處理的歌曲，並在正式錄下任何一軌之前先做個快速的音響測試。本章就是這項準備工作：安裝應用程式、連結存放庫、檢查工具，並載入練習項目。

![以設定錄音室類比設定 GitHub Copilot app](assets/studio-setup-soundcheck.webp)

## 安裝

1. 為您的作業系統[下載並安裝 GitHub Copilot app][app-install]。
2. 開啟應用程式並選取 **Sign in to GitHub**。
3. 使用您的 GitHub 帳戶登入。

    <img src="assets/app-sign-in.webp" alt="登入 Copilot app" width="800" />

4. 在 **Connect your repositories** 畫面，讓所有存放庫保持未選取狀態，然後選取 **Continue**。您將在下一節連結 GitHub Copilot app for Beginners 存放庫的 fork。

應用程式會開啟 **New** 檢視。如果它顯示關於新功能的簡短提示，您可以關閉該提示。接下來，連結您的課程存放庫副本。

## 連結到您的存放庫

> [!NOTE]
> 如果在連結存放庫時 Copilot app 回報找不到 Git，請[安裝 Git](https://git-scm.com/install)，然後重試連結。

1. [在 GitHub 上 fork 課程存放庫][fork-repo-link]。fork 是您自己的存放庫副本，存放在 GitHub 上。

2. 在 Copilot app 中，選取 **Sessions** 旁邊的 **+**（標註 1），然後選取 **Add GitHub repository**（標註 2）。

    ![Sessions 功能表，標註了 + 與 Add GitHub repository](assets/app-add-project.webp)

3. 在搜尋方塊中輸入 `copilot-app-for-beginners`。選取您自己的 fork，也就是以您的 GitHub 使用者名稱開頭的結果，而不是 `github/copilot-app-for-beginners`。應用程式會下載本機副本（複製），並將專案新增到側邊欄。

<details>
<summary>連結存放庫的其他方式</summary>

選取 **Sessions** 旁邊的 **+**，然後選擇符合您現況的選項：

| 如果您有... | 選取 |
|---|---|
| fork 的本機副本 | **Open folder**，然後選取該資料夾 |
| fork 的存放庫 URL | **Clone repository**，在 **Repository URL** 中貼上 URL，然後選取 **Clone** |

</details>

## 在 Terminal 中檢查您的工具

> [!NOTE]
> 使用 GitHub Copilot app 的 **Terminal** 索引標籤來執行命令。使用提示方塊向代理程式傳送要求。

1. 確認側邊欄在 **Sessions** 底下顯示 `copilot-app-for-beginners`。

1. 將滑鼠指向側邊欄中的 `copilot-app-for-beginners` 專案。選取其旁邊出現的 **+**，以啟動專案工作階段。

1. 選取提示方塊下方的工作區選擇器（標註 1），然後選擇 **Current checkout**（標註 2）。

    這會使用您電腦上已有的複本進行設定。您將在後續章節中了解其他選項。

    <img src="assets/app-current-checkout-workspace-selector.webp" alt="Where to work 功能表，標註了工作區選擇器與 Current checkout" width="800" />

1. 送出這個簡短的提示以建立工作階段並進行測試。

    ```text
    Name the top-level folders in this repository. Do not change any files.
    ```

1. 如果側邊面板尚未顯示，請選取 **View** > **Toggle Panel** 以開啟它。

1. 選取 **Terminal**。如果您沒有看到它，請選取 **+**，然後選取 **Terminal**。

1. 在 **Terminal** 中，執行以下每一項版本檢查，確保先決條件已正確安裝。

    ```bash
    git --version
    node -v
    npm -v
    gh --version
    ```

    每個命令都應該顯示版本號碼。對於 `node -v`，請將第一個數字與 [Node.js 下載頁面](https://nodejs.org/en/download)上的 **LTS** 版本比較。如果您的數字較低，請安裝 LTS 版本。

    <details>
    <summary>Windows：npm 因為停用執行指令碼而失敗</summary>

    在 PowerShell 中，`npm -v` 可能會失敗，並顯示無法載入 `npm.ps1`，因為此系統上已停用執行指令碼。若要繼續，請在本課程的每個 npm 命令中，以 `npm.cmd` 取代 `npm`。例如，執行 `npm.cmd -v` 和 `npm.cmd test -- --run`。

    如果您的組織允許，您可以改為讓 PowerShell 執行本機指令碼。執行 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`。如果 PowerShell 要求您確認，請輸入 `Y`。之後本課程中的 `npm` 命令就能照原樣運作。

    </details>

    僅安裝缺少的工具，使用[先決條件](#prerequisites)中的連結，然後再次執行檢查。

1. 設定指令碼使用 GitHub CLI，它有獨立的登入。確認它使用的是您建立 fork 時所用的帳戶：

    ```bash
    gh auth status
    ```

    如果您尚未登入該帳戶，請執行：

    ```bash
    gh auth login
    ```

    選取 **GitHub.com**，然後選取 **Login with a web browser**。登入後，再次執行 `gh auth status`，並在繼續之前確認目前使用中的帳戶。

<a id="seed-the-repository"></a>

## 新增課程練習項目

設定指令碼會在您的 fork 中準備後續章節使用的練習項目：

- **標籤**，用來整理課程任務。
- **議題**，描述您將處理的錯誤與改進項目。
- **練習分支**，包含 Book App 的不同版本，其中有刻意設置、待修正的錯誤。
- **提取要求**，包含待檢視的變更、需要回應的意見回饋留言，以及一個刻意失敗的自動化檢查。

這些是訓練情境，並非您的設定出了問題。刻意設置的錯誤只會存在於練習分支，不會出現在 `main`。指令碼會在 GitHub 上您的 fork 中建立項目，因此在執行之前，您會先預覽它的目標與預計進行的變更。

1. 開啟瀏覽器，前往 github.com 上您剛剛 fork 的存放庫。

1. 選取 **Actions** 索引標籤。

1. 新的 fork 預設會停用工作流程。如果您看到 **Workflows aren't being run on this forked repository**，請選取 **I understand my workflows, go ahead and enable them**。

    啟用工作流程，讓 GitHub 能為練習執行自動化檢查。

1. 回到 Copilot app 中同一個 **Terminal** 索引標籤。從存放庫根目錄（包含 `.github` 與 `samples` 子資料夾的資料夾）執行此命令，以預覽設定而不建立練習項目：

    ```bash
    node .github/scripts/setup-training-scenarios.js --dry-run
    ```

    等待預覽完成。它會以 `Dry run complete. No changes were made.` 結尾。在繼續之前，請確認輸出中的 `Repository:` 行顯示的是您的 fork。

1. 在同一個 **Terminal** 索引標籤中，執行設定指令碼：

    ```bash
    node .github/scripts/setup-training-scenarios.js --yes
    ```

    等待終端機顯示 `Setup complete.`

### 檢查清單

設定完成後，您應該擁有：

- [ ] 一個已連結到 Copilot app 的 fork
- [ ] [課程標籤](../appendices/training-github-scenarios.zh-Hant.md#manual-fallback-create-the-labels)
- [ ] [訓練議題](../appendices/training-github-scenarios.zh-Hant.md#manual-fallback-create-the-seeded-issues)
- [ ] [練習分支](../appendices/training-github-scenarios.zh-Hant.md#manual-fallback-create-practice-branches)
- [ ] [訓練提取要求](../appendices/training-github-scenarios.zh-Hant.md#manual-fallback-create-pull-request-scenarios)

## 進一步了解課程存放庫

回到專案工作階段中的提示方塊。將此要求送交給 Copilot app，而不是 Terminal：

```text
Give me an overview of the copilot-app-for-beginners course repository. Focus on the learning path and the samples/book-app-web folder.
```

**預期輸出：** Copilot app 應該會摘要說明課程結構，並指出 `samples/book-app-web` 是後續練習所使用的網頁範例。

---

## 疑難排解

如果某些功能沒有如預期運作，請檢查下列問題。[疑難排解參考](../appendices/troubleshooting-reference.zh-Hant.md)列出所有章節的問題與解決方法。

<details>
<summary>設定與存取問題</summary>

### 我無法登入

請檢查：

- 您使用的是預期的 GitHub 帳戶
- 您有 Copilot 方案，或是您已選擇繼續使用自己的模型提供者
- 您的組織已保持啟用 **GitHub Copilot app** 原則（與 Copilot CLI 原則分開）
- 如果您的組織使用 `*.ghe.com`，您已選取 **Sign in to GitHub Enterprise Cloud**

### 我看不到存放庫

請檢查：

- 您在 GitHub 上具有該存放庫的存取權
- 您選取了正確的帳戶或組織
- 如果存放庫已經複製，您已嘗試 **Open folder**

### 聊天無法說明存放庫

請檢查：

- 已連結正確的存放庫
- 提示中提到 `copilot-app-for-beginners`
- 應用程式具有讀取專案資料夾的權限

### 指令碼因組織 fork 而停止

指令碼會在變更組織所擁有的存放庫之前停止。只有在您獲授權設定該組織 fork，且已確認 `Repository:` 行顯示正確的目標時，才使用此命令：

```bash
node .github/scripts/setup-training-scenarios.js --yes --allow-shared-repository
```

### 指令碼以「Setup failed」停止

閱讀 `Setup failed` 之後的訊息。修正它所回報的問題，然後再次執行 `node .github/scripts/setup-training-scenarios.js --yes`。指令碼會重覆使用它已建立的標籤、議題、分支與提取要求，因此不會產生重複項目。

### 練習項目未建立

如果缺少議題，請開啟 GitHub.com 上您的 fork。在 **Settings** > **Features** 下，啟用 **Issues**，然後重新執行指令碼。

如果您無法執行指令碼，請在第 02 章之前完成[訓練 GitHub 情境附錄中的手動設定步驟](../appendices/training-github-scenarios.zh-Hant.md#manual-fallback-create-the-labels)。

</details>

---

## 重點整理

1. GitHub Copilot app 是用於代理程式驅動程式設計工作的桌面控制中心。
2. 本課程使用 `samples/book-app-web` 作為主要的範例應用程式路徑。
3. 執行設定指令碼，讓後續章節已備妥練習分支、議題與提取要求情境。

## 下一步

您的錄音室已準備就緒！在下一章，您會先回答一個實際的問題：如果您已在編輯器或終端機中使用 GitHub Copilot，為什麼還要使用 GitHub Copilot app？接著您將導覽介面，並了解不同的工作階段類型與模式。

**[← 返回課程首頁](../README.zh-Hant.md)** | **[繼續前往第 01 章 →](../01-tour-the-app/README.zh-Hant.md)**

[fork-repo-link]: https://github.com/github/copilot-app-for-beginners/fork
[app-install]: https://github.com/features/ai/github-app
