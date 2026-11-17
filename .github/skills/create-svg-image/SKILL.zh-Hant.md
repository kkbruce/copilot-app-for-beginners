---
name: create-svg-image
description: 將點陣圖包裝成 SVG，並在課程截圖上疊加編號的紅色「步驟」圓圈。在執行 /create-svg-image 時使用，或在被要求為圖片加上編號標註徽章（例如指向 UI 控制項、內含「1」的紅色圓圈），且仍須能在 GitHub README 上呈現時使用。
---

# 建立 SVG 圖片（編號步驟徽章）

使用此技能來取得一張截圖或圖片，並產生一個 **SVG**，其中顯示該
圖片，且圖片上方疊加一個 **編號的紅色圓圈**。這用於新增
「步驟 1 / 步驟 2」的標註徽章，指向課程
截圖中的特定 UI 控制項。

呼叫端一律會提供三項內容：

1. **要使用的圖片** — 存放庫中的 `.webp`、`.png` 或 `.jpg` 檔案。
2. **編號圓圈的位置** — 圓圈在圖片上的位置，
   以圖片原生的像素座標表示（X 從左側起算，Y 從頂端起算）。
3. **要套用的編號** — 顯示在圓圈內的數字／短文字。

選用輸入：圓圈 `--radius`、填色 `--color`、`--text-color`，以及 `--out`
路徑。

## 為何產生 SVG（而非內嵌 HTML 或直接編輯圖片）

GitHub 會將 Markdown 中參考的 SVG（`![](file.svg)`）呈現於
**沙箱化的 `<img>`** 內。在該模式下，GitHub：

- **會移除內嵌的 `<svg>`**（來自 README HTML），因此您無法將原始的 `<svg>`
  標記貼到 Markdown 中，並且
- **拒絕擷取外部／相對路徑的** `<image href="photo.webp">`，因此僅指向點陣圖的 SVG
  會顯示空白的照片。

因此可靠的做法是產生一個 SVG 檔案，它會 **將點陣圖以
`data:` base64 URI 內嵌**，並在上方繪製 `<circle>` + `<text>`。照片會
在 GitHub 上正常顯示，而徽章仍可作為純 SVG 標記進行編輯。（使用影像編輯器將數字燒錄
到 PNG/webp 中也可行，但如此一來數字就
不再是可編輯的文字 — 建議使用 SVG。）

## 協助指令碼

[`sample_codes/create-svg-badge.sh`](sample_codes/create-svg-badge.sh) 負責
完成整個工作：偵測圖片大小與 mime 類型、對點陣圖進行 base64 編碼、寫出
SVG、在可用時以 `xmllint` 驗證，並在有可用的轉譯器且啟用預覽時
轉譯出 PNG 預覽。

```bash
# 必要項：--image、--x、--y、--number
bash .github/skills/create-svg-image/sample_codes/create-svg-badge.sh \
  --image 02-sessions-worktrees-context/assets/app-create-from-icon.webp \
  --x 710 --y 72 --number 1
# -> 寫入 assets/app-create-from-icon-step1.svg 並印出 PREVIEW png 路徑
```

實用的選項：`--radius`（預設為圖片寬度的約 2.5%，使徽章在畫面上維持
一致的大小，見下文）、`--color "#d1242f"`（GitHub 危險紅）、
`--out PATH`、`--grid`（座標尋找工具，見下文）、`--no-preview`。

## 工作流程

1. **找出圓圈座標**，若呼叫端給的是大致的位置（「在
   Create from 圖示的左側」）而非精確的像素。請以 `--grid`
   模式執行指令碼，它會在圖片上疊加有標籤的座標格線，並轉譯出
   預覽，**且不會對存放庫寫入任何內容**：

   ```bash
   bash .github/skills/create-svg-image/sample_codes/create-svg-badge.sh \
     --image <path> --number 1 --grid
   ```

   檢視印出的 `GRID_PREVIEW` PNG，讀取綠色（X）與洋紅色（Y）標籤
   以選定目標像素，然後繼續。座標位於圖片原生的
   像素空間（SVG 的 `viewBox` 等於圖片的像素尺寸）。

2. **產生帶徽章的 SVG**，使用選定的 `--x`、`--y` 與 `--number`：

   ```bash
   bash .github/skills/create-svg-image/sample_codes/create-svg-badge.sh \
     --image <path> --x <N> --y <N> --number <N>
   ```

3. **透過檢視預覽來驗證。** 以圖片檢視工具開啟印出的 `PREVIEW` PNG，
   並確認圓圈位於呼叫端想要的位置。注意：macOS 的
   `qlmanage` 會將預覽填補為正方形畫布 — 只有頂端的長條（圖片的
   實際高度）才是真正的圖片；下方的白色區域是填補，不是輸出。

4. **視需要調整。** 以新的值重新執行來微調：較小的 `--x` 會將
   圓圈向左移，較大的 `--x` 向右；`--y` 使其上移／下移；`--radius` 會調整
   其大小（字型會自動縮放）。反覆調整直到位置正確。

5. **將其接入章節**（僅在呼叫端要求變更 README 時）：
   改為參考新的 `.svg` 而非原始的點陣圖，並保留具描述性的替代
   文字：

   ```markdown
   ![Create from branch](assets/app-create-from-icon-step1.svg)
   ```

## 慣例

- 預設輸出位於 **來源圖片旁**，名稱為
  `<stem>-step<number>.svg`，符合此存放庫各章節的 `assets/` 配置。
- 請將 **原始的點陣圖** 保留在存放庫中作為可編輯的來源；
  即使 README 現在指向 `.svg`，它仍是輸入。
- 預設徽章樣式：實心的 GitHub 危險紅 `#d1242f`、白色數字、細白色
  外框，使其在淺色或深色截圖上都清晰可讀。
- **請讓每個產生的 SVG 的徽章直徑保持一致。** 半徑
  預設為 **圖片寬度的約 2.5%**（白色外框與數字會隨之
  縮放）。由於 GitHub 會將每張截圖縮放至 README 欄位寬度，這使
  每個徽章無論擷取畫面的解析度為何，轉譯後的視覺大小都相同。
  這就是為何 936px 寬擷取畫面上的 `radius 24` 徽章與 2240px 寬擷取畫面上的
  `radius 56` 徽章看起來完全相同。僅在刻意的一次性需求時才傳入 `--radius`；
  否則請讓預設值維持一致。
- 每個檔案一個圓圈符合所需的輸入（圖片 + 一個位置 + 一個
  編號）。對於多步驟的圖，可以產生個別的 `-step1`、`-step2`
  檔案，或手動在 SVG 中加入額外的 `<circle>`/`<text>` 配對。

## 安全性與注意事項

- 預覽會轉譯到暫存目錄（`$TMPDIR/create-svg-badge`），絕不會寫入存放庫。
- `--grid` 不會對存放庫寫入任何內容；它純粹是用來尋找座標的預覽。
- 截圖可能包含帳戶名稱、私人存放庫名稱或路徑 — 請以通用方式描述
  UI，並在回報位置時不要洩露私人資料。

## 相關技能

- **comic-strip-site** — 透過將 AI 產生的角色畫格包裝在內嵌 SVG
  （漫畫框、帶尾巴的對話
  泡泡、終端機指令列）中，建置完整的網路漫畫風格網站。與此技能共用 SVG 疊加於點陣圖的技巧
  及 `--grid` 座標尋找工作流程，但用於敘事畫格
  與重複出現的卡通角色，而非單一的編號步驟徽章。
