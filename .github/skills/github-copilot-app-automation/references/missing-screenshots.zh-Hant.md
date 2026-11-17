# Capturing the Course Screenshots

章節 README 是仍需要哪些 app 螢幕擷圖的**唯一事實來源**。
每個待處理的擷圖都是一則 HTML 註解：

```html
<!-- app-screenshot: <description> -->
```

此技能中沒有任何關於個別擷圖的寫死內容 — 它們是從這些註解
**動態探索**而來，因此隨著各章節新增、
編輯或移除預留位置，此工作流程仍能持續運作。

> 從儲存庫根目錄開始，並設定：
> `SK=.github/skills/github-copilot-app-automation/sample_codes/macos-accessibility`

在每張螢幕擷圖之前，先準備新的 persona 執行個體（標準 1920x1080 點視窗、150% 縮放）。
請傳入訓練分支的本機複本：

```bash
COPILOT_PID="$(bash "$SK/prepare-persona.sh" \
  demo "$HOME/copilot-app-for-beginners" 60)"
```

請保留此處理序 ID。它可避免擷取選到另一個開啟的 Copilot
視窗。

## 1. Discover what's pending

```bash
bash "$SK/screenshots.sh" list
```

列出每一個剩餘的 `<!-- app-screenshot: ... -->`，以及其檔案、行號與
描述。隨著擷圖被內嵌，它們會自動從清單中消失。

## 2. Do them one at a time

擷取是半自動的（由人員操作 app；CLI 沒有
`navigate_to`），因此每次迴圈處理一張擷圖：

```bash
# a) 挑選下一個待處理的擷圖（會印出描述、衍生的 slug、assets 目錄，
#    以及該擷圖確切的擷取/內嵌命令）。
bash "$SK/screenshots.sh" next          # 或：next 13   以指定索引

# b) 閱讀周圍的課程指示。判斷影像是否顯示
#    讀者必須依序選取的兩個或更多控制項。請使用 `next` 所印出的檔案與
#    行號；預留位置尚未有影像名稱。
python3 "$SK/screenshot-context.py" <chapter>/README.md --line <line>

# c) 準備專用的 demo persona，並讓該執行個體進入所需
#    狀態。對於資料擷圖，請先執行 Node.js 設定指令碼。
COPILOT_PID="$(bash "$SK/prepare-persona.sh" \
  demo "$HOME/copilot-app-for-beginners" 60)"

# d) 擷取（PNG + WebP 存入章節的 assets/）。請使用步驟 (a) 的 slug，
#    或任何您偏好的名稱：
bash "$SK/capture-window.sh" <assets_dir> <slug> 40 "$COPILOT_PID"

#    對於顯示多個有順序動作的單一影像，請為每個
#    動作附加一個標註。請使用最終的 1920x1080 影像座標（locate-copilot-element
#    會印出它們）。單一項目請使用 --box，細節區域請使用 --crop：
bash "$SK/capture-window.sh" <assets_dir> <slug> 40 "$COPILOT_PID" \
  --callout 1:X:Y --callout 2:X:Y

# e) 確認大頭貼仍在，設定檔文字顯示「Copilot Dev」，且
#    相符的個人儲存庫擁有者、路徑或分支顯示「copilotdev」。
#    然後檢閱是否有其他私人資料。確認標註符合
#    指示順序，且沒有遮住控制項標籤或圖示。

# f) 以內嵌取代預留位置：
bash "$SK/screenshots.sh" embed <file> <line> <slug>.webp "<alt text>"
```

`screenshots.sh next` 會印出目前擷圖可直接貼上的命令。
隨時重新執行 `screenshots.sh list` 以查看還剩下什麼。

僅在一張螢幕擷圖顯示至少兩個讀者必須依序選取的控制項時，才使用標註。
請勿將它們加入單一動作的螢幕擷圖、
輸出範例、終端機佐證或概念插圖。

## Which shots can be done when

請依據每張擷圖的描述來判斷 — 不要依賴儲存的清單：

- **App 外觀擷圖**（設定索引標籤、撰寫區、選擇器、新增自動化
  表單）：隨時可擷取；不需要種子資料。請讓私人面板（例如
  側邊欄工作階段清單）不在畫面內，或從乾淨的帳戶擷取。
- **資料擷圖**（Issues 與 Pull requests 檢視、issue/PR 詳細資料、差異、
  失敗的檢查、執行歷程記錄、即時工作階段或 canvas）：需要已執行
  `node .github/scripts/setup-training-scenarios.js --yes` 的**訓練
  分支**，並使用已清理的帳戶。
- **受原則/組建限制的擷圖**（Agent Merge、雲端自動化、canvas 撰寫）：
  可用時再擷取；否則將預留位置保留為僅供示範。

## Filenames

在執行階段從每張擷圖的描述衍生（`screenshots.sh next` 會顯示
slug）；可依擷圖傳入您自己的名稱來覆寫。擷取的 `.webp` 會存入
該章節的 `assets/`，而章節 README 會參考該 `.webp`。請依儲存庫的資產慣例保留或
捨棄 `.png` 來源。

## Privacy reminder

真實的擷取內容會暴露即時的專案/工作階段名稱、提示、差異，以及可能的
權杖（請參閱 [app-ui-map.zh-Hant.md](app-ui-map.zh-Hant.md) 中的遮蔽說明）。請一律從已清理的帳戶擷取，
並在提交之前檢閱每張影像。
