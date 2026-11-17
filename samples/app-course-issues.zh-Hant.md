# Book App Web Course Issues

將這些 issue 草稿用於應用程式工作階段、分支練習和引導式修正。範例應用程式預設是穩定的，因此每個錯誤情境都包含一個小型的迴歸設定步驟。請在訓練分支上加入該迴歸，然後讓 Copilot 診斷並修正它。

## Issue 1: Make search case-insensitive

標籤：`bug`, `good first issue`, `book-app-web`

書籍搜尋應該比對標題和作者，而不論字母大小寫。

### 訓練分支設定

建立一個名為 `practice-search-case-bug` 的分支，然後在 `samples/book-app-web/src/App.tsx` 中引入迴歸：

```diff
- const normalizedSearchTerm = filters.searchTerm.trim().toLowerCase();
+ const normalizedSearchTerm = filters.searchTerm.trim();

  return bookList.filter((book) => {
    const matchesSearch =
      normalizedSearchTerm.length === 0 ||
-     book.title.toLowerCase().includes(normalizedSearchTerm) ||
-     book.author.toLowerCase().includes(normalizedSearchTerm);
+     book.title.includes(normalizedSearchTerm) ||
+     book.author.includes(normalizedSearchTerm);
```

### 重現步驟

1. 開啟 `samples/book-app-web`。
2. 執行 `npm run dev`。
3. 搜尋 `hobbit`。

### 預期結果

`The Hobbit` 出現在結果中。

### 學習者目標

要求 Copilot 找出搜尋邏輯、撰寫或更新測試，並進行最小且安全的修正。

## Issue 2: Keep unread stats correct when filters are active

標籤：`bug`, `tests`, `book-app-web`

統計資料應該描述套用篩選後目前顯示的書籍。

### 訓練分支設定

建立一個名為 `practice-unread-count-bug` 的分支，然後在 `samples/book-app-web/src/App.tsx` 中引入迴歸：

```diff
- <ReadingStats books={filteredBooks} />
+ <ReadingStats books={books} />
```

這會使統計資料忽略作用中的篩選條件。

### 重現步驟

1. 將狀態篩選條件設為 `Unread`。
2. 比較書籍卡片與統計資料列。

### 預期結果

未讀數量與可見的未讀卡片相符。

### 學習者目標

練習要求 Copilot 追蹤從 `App.tsx` 到 `ReadingStats.tsx` 的 props。

## Issue 3: Improve the empty state copy

標籤：`accessibility`, `copy`, `book-app-web`

空狀態應該對初學者和螢幕閱讀器使用者都清楚易懂。

### 訓練分支設定

建立一個名為 `practice-empty-state-copy` 的分支，然後在 `samples/book-app-web/src/App.tsx` 中讓空狀態變得較沒有幫助：

```diff
- <h2>No matching books found</h2>
- <p>Try a different search term, genre, or reading status.</p>
+ <h2>No results</h2>
+ <p>Try again.</p>
```

### 重現步驟

1. 搜尋不符合任何書籍的文字。
2. 檢閱空狀態。

### 預期結果

訊息說明找不到相符的書籍，並建議變更篩選條件。

### 學習者目標

使用 PR 交談留言要求更清楚的文案，然後要求 Copilot 處理該留言。

## Issue 4: Polish book card spacing and responsive layout

標籤：`ui`, `responsive`, `book-app-web`

書籍卡片應該具有清楚的層次結構、舒適的間距，以及實用的行動裝置版面配置。

### 訓練分支設定

建立一個名為 `practice-card-polish` 的分支，然後以預設應用程式作為基準。這是視覺改善情境，而不是測試失敗的錯誤。設定指令碼可能會新增一則無害的 README 備註，讓該分支可以作為訓練 PR 開啟。

### 重現步驟

1. 在瀏覽器中開啟應用程式。
2. 將檢視區調整到 560px 以下。
3. 檢查卡片間距和狀態標籤。

### 預期結果

卡片保持易讀，且控制項仍然容易使用。

### 學習者目標

使用瀏覽器驗證或 Pick and Polish 練習，在接受之前比較視覺變更。

## Issue 5: Simulate a failing stats test for CI practice

標籤：`ci`, `tests`, `book-app-web`

預設應用程式應該通過測試。對於此情境，請建立一個錯誤地變更最愛書籍計數方式的訓練分支，然後讓 CI 在統計測試上失敗。

### 建議的刻意迴歸

將最愛數量改為只計算已讀的最愛書籍。這應該會使預先設定的 `stats.test.tsx` 預期失敗，因為未讀的最愛書籍同樣是有效的最愛。

在 `samples/book-app-web/src/components/ReadingStats.tsx` 中套用此變更：

```diff
- const favoriteCount = books.filter((book) => book.isFavorite).length;
+ const favoriteCount = books.filter((book) => book.isFavorite && book.isRead).length;
```

### 學習者目標

練習使用 Copilot 檢查失敗的檢查、說明迴歸，並還原正確的最愛數量。
