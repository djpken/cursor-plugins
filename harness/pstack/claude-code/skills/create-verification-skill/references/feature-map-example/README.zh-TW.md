<a id="notes-verification-map"></a>

# Notes 驗證地圖

此目錄是維護中的來源，用於驗證 Notes 的使用者端行為。在操作應用程式之前，請先閱讀索引，然後使用對應的功能檔案作為操作指南。

<a id="baseline-preconditions"></a>

## 基準前置條件

- 在 `http://127.0.0.1:4173` 啟動 Notes，並使用一次性的資料目錄。
- 設定 `NOTES_DATA_DIR=/tmp/notes-verify-$RUN_ID`，以確保並行執行不會共用狀態。
- 建立標題為 `Quarterly plan` 和 `Grocery list` 的筆記。
- 將 `control-notes` 和 `notes` CLI 放在 `PATH` 上。
- 執行 `control-notes doctor`，並要求符合預期的 URL、資料目錄和建置修訂版本。
- 切勿操作非由本次驗證執行所啟動的執行個體。

<a id="driving-conventions"></a>

## 操作慣例

- 除非前置條件另有說明，否則每個操作指南都應從基準狀態開始。
- 優先使用 ARIA 角色和可存取名稱，而非 CSS 選擇器或 DOM 位置。
- 將每個指令視為字面意義。保持引號中的名稱和旗標不變。
- 透過 `control-notes browser` 執行瀏覽器操作。
- 透過 `control-notes cli -- <command>` 執行終端機操作。
- 在變更後還原已建立的資料。清理期間請勿移除證明產物。

<a id="proof-and-skip-reporting"></a>

## 證明與跳過報告

- 擷取使用者操作和產生的狀態，而不僅是最終螢幕。
- UI 證明包含 ARIA 快照和顯示應用程式識別資訊的螢幕截圖。
- CLI 證明包含指令、stdout、stderr 和結束程式碼。
- 變更證明包含對儲存值的唯讀第二檢視。
- 記錄每個產物所使用的功能 ID 和進入點。
- 報告無法到達的路徑時，需附上嘗試的指令和未滿足的前置條件。
- 請勿將跳過的進入點報告為已透過其他路徑驗證。

<a id="feature-entry-contract"></a>

## 功能進入點合約

每個功能檔案以 H1 標題和一段描述使用者可見行為的文字開始。接著，它會依序使用恰好四個 H2 章節。

1. `Sub-features` 列出簡短 ID，每個行為對應一行。
2. `How to get to it (user POV)` 列出所有使用者進入點。
3. `Driving it with <harness>` 以 `Preconditions:` 開始，並使用帶標籤的專案符號，將每個使用者操作與精確的指令和可觀察的結果配對。
4. `Gotchas` 列出可能浪費或使驗證執行無效的陷阱。

將實作細節排除在地圖之外。僅命名使用者路徑、穩定控制程式碼、必要狀態、指令和可觀察的證明。

<a id="features"></a>

## 功能

- [建立筆記](./create-note.md) 涵蓋瀏覽器和 CLI 的建立、取消、持久化和清理。
- [搜尋筆記](./search.md) 涵蓋工具列、鍵盤和 CLI 搜尋，包括匹配、空值和清除狀態。
