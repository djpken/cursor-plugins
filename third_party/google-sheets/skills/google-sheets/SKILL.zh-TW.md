---
name: google-sheets
description: "使用 google-sheets 工具建立和編輯 Google Sheets - 值、格式、圖表、條件格式、結構、驗證、命名和保護範圍。在建立工作表或儀錶板、格式化細胞、新增或修正圖表，或重新組織分頁和行時使用。"
---

<a id="google-sheets-authoring"></a>

# Google Sheets 編寫

<a id="two-coordinate-systems-on-purpose"></a>

## 兩個座標系統，刻意設計

- VALUE 工具（`read_range`、`write_range`、`append_rows`、`clear_range`）
  使用 TAB 標題加上 A1 細胞：`{sheet: "Data", cells: "A1:B4"}`。
- STRUCTURE/FORMAT 工具（`format_cells`、`set_borders`、`merge_cells`、
  `add_chart`, `add_conditional_formatting`, `insert_rows_or_columns`,
  `delete_rows_or_columns`, `freeze_rows_or_columns`, `sort_range`,
  `set_data_validation`, `set_basic_filter`, `add_banding`,
  `set_column_width`、`rename_sheet_tab`、`delete_sheet_tab`）使用
  數字 `sheetId`。

`get_spreadsheet` 返回兩者（每個分頁的標題和 sheetId）且不含細胞
資料 - 先呼叫它並保留對映。新分頁的 sheetId 在
`add_sheet_tab` 的回應中（`replies[0].addSheet.properties.sheetId`）。

<a id="sequencing-a-styled-sheet-values-first-then-format-chart-last"></a>

## 建立有樣式的工作表順序：先值，再格式，圖表最後

1. `create_spreadsheet`（可選用命名分頁；它無法指定
   資料夾 - 之後用 google-drive `google_drive_move_or_rename` 移動它）。
2. 以 `write_range` 處理資料，當值包含 `parseInput: true`
   （預設 false 將所有內容儲存為純文字 - 這是"圖表空白"錯誤的常見來源）。
   格式像真正的儀錶板，不是裸表格：
3. 格式像真正的儀錶板，而非簡陋的表格：
   - `format_cells` 標頭行。
     patterns on value columns (`$#,##0`, `0.0%`).
   - `freeze_rows_or_columns` 在表格範圍上 - 篩選下拉是
   - `set_basic_filter` 在表格範圍之上 - 篩選下拉選單才是
     make it read as a data table.
   - `set_borders`（外層比內層粗）或 `add_banding` 用於
     alternating row colors; `set_column_width` so nothing clips.
   - `add_conditional_formatting` 最後，在資料存在之後。
4. `add_chart` 最後，在資料存在之後。

<a id="charts"></a>

## 圖表

- 在 `add_chart` 中，第一個範圍是領域（x軸 / 圓餅圖示籤）且
  錯誤或反向範圍是最常見的圖表錯誤。所有範圍位於
  錯誤或反向的範圍是最常見的圖表錯誤。所有範圍位於
  圖表所在的同一工作表。`headerCount: 1` 使用第一個單元格作為
  將圖表放在表格旁邊或下方的空細胞上，不要放在
- 它所繪製的資料之上。
  它所繪製的資料。
- 列出每個分頁的圖表（chartId、標題），`get_spreadsheet` 取代
  圖表的規格並保留其位置，`update_chart` 移除一個。絕不要
  圖表的規格保持其位置，`delete_chart` 移除一個。絕不新增
  命名和保護範圍

<a id="named-and-protected-ranges"></a>

## 命名與保護範圍

- `add_named_range`）；用 `=SUM(Budget)` 寫公式。名稱
  的 `=SUM(B2:B13)`）；用 `parseInput: true` 寫公式。名稱
  不能看起來像單元格參考。
- `protect_range` 保護標題與公式單元格（`warningOnly: true` 預設
  警告編輯者；`false` 鎖定它們）。在最後一次寫入
  該範圍之後進行保護，否則你自己的寫入會與保護衝突。
- `get_spreadsheet` 列出兩者，使用 ID `delete_named_range` 和
  `unprotect_range` 需要。

<a id="pitfalls"></a>

## 任何地方都沒有版本保護：最後寫入勝出。在寫入前讀取，當

- 協作者可能正在編輯。
  協作者可能正在編輯。
- `merge_cells` 重寫細胞位置；在新增引用範圍的公式之前排序，
- `sort_range` 重寫單元格位置；在新增引用範圍的公式之前排序，
  並從排序範圍中排除標題行。
- `insert_rows_or_columns` / `delete_rows_or_columns` 位置以 1 為基準
  （A=1）；刪除是立即生效且無法透過 API 回復。
- `find_replace_sheet` 在公式內搜尋。
  使用 `searchFormulas: true` 的公式。
- 整工作表讀取可能超出位元組上限；在未確定大小的工作表上限制 `cells`（例如 "1:2000"）
  並在截斷時遵循 `nextCells`。

<a id="verify-your-work"></a>

## 驗證你的工作

`read_range` 用於值（FORMATTED_VALUE 顯示使用者看到的；
UNFORMATTED_VALUE 原始數字 - 用它確認數字是數字，
保護。google-drive `get_spreadsheet`（pdf 渲染每個分頁
保護。google-drive `google_drive_export_file`（PDF 渲染每個工作表
包括圖表和格式）當視覺結果很重要時。
