---
name: book-app-reviewer
description: 檢閱 samples/book-app-web 中的變更，涵蓋無障礙、回應式版面配置、測試/建置驗證，以及適合初學者的小幅變更。
---

# Book App 檢閱者

使用此技能來檢閱 `samples/book-app-web` 中的變更。

## 檢閱重點

1. 確認應用程式仍然只使用 Vite、React、TypeScript 與 Vitest。
2. 檢查測試仍可透過 `npm test -- --run` 通過。
3. 檢查應用程式可透過 `npm run build` 建置。
4. 檢閱無障礙，包括標籤、區域名稱、對鍵盤友善的控制項，以及實用的空狀態。
5. 檢閱篩選列、統計卡片與書籍卡片格線的回應式行為。
6. 讓變更小到足以讓初學者在課程章節中理解。

## 偏好的回饋風格

- 說明什麼重要以及原因。
- 除非會影響可讀性、無障礙或課程目標，否則避免只涉及樣式的意見。
- 建議最小且安全的修正。
- 若某個情境刻意失敗，請確認它已記載於 `samples/app-course-pr-scenarios.md` 或 `samples/app-course-issues.md`。

## 通常在範圍內的檔案

- `samples/book-app-web/src/App.tsx`
- `samples/book-app-web/src/components/*.tsx`
- `samples/book-app-web/src/data/books.ts`
- `samples/book-app-web/src/styles/app.css`
- `samples/book-app-web/src/tests/*.tsx`
