---
name: "Traffic Updater"
description: "每週蒐集存放庫流量資料（檢視次數與不重複訪客）。將上週的每日數字附加到 CSV 檔案。"
on:
  schedule: weekly on monday
  workflow_dispatch:
checkout:
  # create_pull_request 需要 origin/main 才能找到合併基底，在分支執行時亦然。
  fetch: ["main"]
tools:
  bash: ["date"]
  edit:
  github:
    toolsets: [repos]
mcp-scripts:
  fetch-traffic:
    description: "從 GitHub API 擷取此存放庫最近 14 天的流量檢視資料。傳回包含 views 陣列的 JSON，其中每天包含 timestamp、count 與 uniques。"
    run: |
      gh api repos/$GITHUB_REPOSITORY/traffic/views
    env:
      GH_TOKEN: "${{ secrets.GH_AW_GITHUB_TOKEN }}"
safe-outputs:
  allowed-domains:
    - github.com
  noop:
    report-as-issue: false
  create-pull-request:
    labels: [automated-update, traffic-data]
    title-prefix: "[bot] "
    base-branch: main
    fallback-as-issue: false
    protected-files: allowed
    allowed-files:
      - ".github/uvs.csv"
      - ".github/views.csv"
    github-token: ${{ secrets.GH_AW_GITHUB_TOKEN }}
---

# 蒐集每週存放庫流量

您是 **copilot-app-for-beginners** 存放庫的流量蒐集機器人。您的工作是從 GitHub API 擷取上週的流量數字，並將其附加到兩個 CSV 檔案。

## 定義

- **不重複訪客** 記錄於 `.github/uvs.csv`
- **總檢視次數** 記錄於 `.github/views.csv`
- 兩個檔案都使用 `"MM/DD",count` 格式 — 每天一行，沒有標頭列。
- 此工作流程可在任何一天觸發。它一律從檔案中斷之處繼續。

## 步驟 1 — 判斷最後記錄的日期

讀取 `.github/uvs.csv`（或 `.github/views.csv` — 兩者應保持同步）的最後一行。剖析 `"MM/DD"` 日期以判斷已記錄的最後一天。日期的年份請假設為今年。

若兩個檔案皆為空，則將起始日期視為 14 天前（GitHub API 所提供的最大範圍）。

## 步驟 2 — 擷取流量資料

呼叫 `fetch-traffic` 工具（不需要輸入）。它會傳回包含 `views` 陣列的 JSON，其中的物件含有最近 14 天每一天的 `timestamp`、`count` 與 `uniques`。

## 步驟 3 — 僅篩選出新的日期

從 API 回應中，僅保留日期 **晚於** 步驟 1 所得最後記錄日期的項目。

也請排除 **今天的日期**，因為當天尚未結束，數字將不完整。

將每個保留的日期格式化為 `"MM/DD"`（月份與日期補零，不含年份）。

若沒有要新增的日期，請在此停止，並回報沒有可用的新資料。

## 步驟 4 — 附加新的資料列

將新的資料列附加到每個檔案的結尾，並保持現有資料不變：

- **`.github/uvs.csv`** — 為每個新的日期附加 `"MM/DD",{uniques}`
- **`.github/views.csv`** — 為每個新的日期附加 `"MM/DD",{count}`

資料列應依時間順序排列（最早的日期在前）。

## 步驟 5 — 開啟 pull request

建立以 `main` 分支為目標的 pull request。PR 標題應摘要日期範圍，例如：

> Add traffic data for week of MM/DD – MM/DD

PR 本文應包含：

1. 所蒐集的日期範圍
2. 該週的總檢視次數與不重複訪客
3. 顯示每日明細的簡短表格或清單
