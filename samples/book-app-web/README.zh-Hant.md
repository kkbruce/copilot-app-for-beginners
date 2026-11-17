# Book App Web

這是「GitHub Copilot app 初學者課程」的標準範例應用程式。

它刻意保持精簡，讓學習者可以專注於應用程式工作流程，而不是後端設定。此應用程式使用 Vite、React、TypeScript 和 Vitest。

## 執行應用程式

請使用目前的 Node.js LTS。

```bash
npm install
npm run dev -- --host 127.0.0.1 --port 5173
```

## 驗證變更

```bash
npm test -- --run
npm run build
```

## 課程情境

應用程式一開始處於穩定狀態。測試預設應該會通過。

課程仍需要可預期的 issue 和 PR 練習，因此預先設定的情境文字位於：

- `../app-course-issues.md`
- `../app-course-pr-scenarios.md`

這些檔案描述了學習者可以在分支中引入或修正的錯誤，而不會使預設範例失敗。
