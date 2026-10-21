<a id="search-notes"></a>

# 搜尋筆記

搜尋功能讓使用者可以透過標題或內文找到筆記、檢視符合條件的筆記，並區分「沒有符合專案」與「搜尋功能無法使用」兩種情況。

<a id="sub-features"></a>

## 子功能

- `search-open` 從每個支援的瀏覽器入口開啟搜尋。
- `search-match` 回傳標題與內文的符合專案，且不變更筆記資料。
- `search-open-result` 在筆記編輯器中開啟結果。
- `search-empty` 針對沒有符合專案的查詢顯示完整的空白狀態。
- `search-clear` 移除查詢並還原最近筆記檢視。
- `search-cli` 從終端機回傳相同的符合筆記。

<a id="how-to-get-to-it-user-pov"></a>

## 如何進入（使用者視角）

- 在瀏覽器工具列中選擇 `Search` 按鈕。
- 在瀏覽器中，當焦點不在可編輯欄位時，按下 `/`。
- 在終端機中執行 `notes search <query>`。

<a id="driving-it-with-control-notes"></a>

## 使用控制筆記進行操作

前置條件：

- Notes 在 `http://127.0.0.1:4173` 處於健康狀態。
- 一次性資料目錄包含 `Quarterly plan`，其內文為 `Draft budget`。
- `control-notes doctor` 回報預期的 URL 與資料目錄。

- **工具列入口。** 選擇 `Search` 按鈕。執行 `control-notes browser click --role button --name "Search"`。出現名為 `Search notes` 的對話方塊，且焦點位於其搜尋框中。
- **鍵盤入口。** 關閉對話方塊，將焦點移至頁面，然後按下 `/`。執行 `control-notes browser press --key "/"`。出現相同的對話方塊，且頁面不會插入斜線。
- **標題符合。** 輸入 `quarterly`。執行 `control-notes browser fill --role searchbox --name "Search notes" --value "quarterly"`。`Search results` 清單包含 `Quarterly plan`，且不包含 `Grocery list`。
- **內文符合。** 將查詢替換為 `budget`。執行 `control-notes browser fill --role searchbox --name "Search notes" --value "budget"`。結果 `Quarterly plan` 保持可見，並顯示內文符合的摘要。
- **開啟結果。** 選擇 `Quarterly plan`。執行 `control-notes browser click --role link --name "Quarterly plan"`。對話方塊關閉，編輯器標題顯示為 `Quarterly plan`。
- **空白狀態。** 重新開啟搜尋並輸入 `volcano`。執行 `control-notes browser fill --role searchbox --name "Search notes" --value "volcano"`。搜尋完成後，出現名為 `No matching notes` 的狀態訊息。
- **清除查詢。** 選擇 `Clear search`。執行 `control-notes browser click --role button --name "Clear search"`。搜尋框為空，且 `Recent notes` 區域取代結果清單。
- **CLI 符合。** 從終端機進行搜尋。執行 `control-notes cli -- notes search "quarterly" --format json`。結束程式碼為 `0`，且標準輸出包含一個標題為 `Quarterly plan` 的物件。
- **CLI 未符合。** 搜尋一個不存在的值。執行 `control-notes cli -- notes search "volcano" --format json`。結束程式碼為 `0`，且標準輸出為 `[]`。
- **證明。** 擷取已填入結果的狀態。執行 `control-notes browser snapshot --aria --path artifacts/search/results.aria.txt` 與 `control-notes browser screenshot --path artifacts/search/results.png`。兩份產出物均識別出 Notes、查詢以及 `Quarterly plan`。

<a id="gotchas"></a>

## 注意事項

- 當編輯器或搜尋框具有焦點時按下 `/`，會插入文字而非開啟搜尋。
- 結果會在短暫的防抖延遲後更新。請等待結果清單或空白狀態出現，而非使用固定的睡眠時間。
- 已歸檔的筆記會被排除，除非使用者啟用 `Include archived`。
- CLI 預設為人類可讀的輸出格式。請使用 `--format json` 以獲得穩定的斷言。
- 開啟結果會變更瀏覽器狀態。在證明另一個查詢之前，請重新開啟搜尋。
