---
name: google-docs
description: "使用 google-docs 工具來撰寫和編輯 Google Docs：結構化寫作、字元和段落樣式、列表、表格、圖片、頁首/頁尾。在建立格式化檔案、編輯檔案內容或套用樣式時使用。"
---

<a id="google-docs-authoring"></a>

# Google Docs 撰寫

<a id="locate-text-never-compute-indexes-the-1-source-of-bugs"></a>

## 定位文字，切勿自行計算索引（這是錯誤的首要來源）

Docs 編輯以零起始的 UTF-16 索引範圍處理單一分頁內的內容，且每次
寫入都會使後方索引位移。猜測或過期的索引會靜默地影響錯誤的
段落：典型症狀是兩個列表合併為一個連續
編號。這些工具消除了索引計算的需求：

- `find_text` 會返回最新的 `{start, end}` 範圍以匹配確切文字：這是
  `apply_text_style`、`apply_paragraph_style`、
  `delete_content` 和 `set_paragraph_format` 索引的直接來源。先定位，再編輯，
  在連續的呼叫中執行。
- `create_list` 接受 `firstItemText`/`lastItemText` 錨點並自行
  解析範圍：指定第一個和最後一個專案段落，索引就不會
  需要你處理。每個列表使用一個 `create_list`，搭配正確的
  預設（BULLET_* 用於專案符號，NUMBERED_* 用於數字）；切勿將一個
  呼叫跨越兩個視覺上分開的列表。

當範圍必須來自結構而非文字片段（例如樣式化整個
段落、處理表格），從 `read_document` 元素讀取：一個
段落的 `{start, end}` 是段落層級工具的範圍。結束索引是
排除性的：少一個會漏掉最後一個字元，多一個會樣式化
文字片段後面的字元（典型的斜體或連結的差一錯誤）。

當原始索引不可避免時：先使用 `read_document`，計算一次編輯，
傳入 `requiredRevisionId`，套用，並在下一次位置
編輯前重新讀取。在步驟之間重新讀取成本太高時，將多部分位置工作
從後往前排序（最高索引優先）；較早的範圍保持有效。

<a id="creating-a-document"></a>

## 建立檔案

`create_document` 建立空白檔案（其 API 忽略內容），因此需規劃：

- 內容事先已知：優先使用 google-drive `google_drive_create_file`
  搭配 Markdown（`target_mime_type: ...document`），可一次匯入標題、
  列表和表格，並可指定目標資料夾。
- 逐步建立：`create_document`，然後 `insert_text` 搭配 `index`
  省略以 APPEND 每個段落；追加模式自行讀取尾部並
  自動受版本保護。`paragraphStyle` 樣式化新段落
  並 `bold`/`italic` 在同一呼叫中樣式化插入的文字，因此
  結構良好的檔案是一系列追加操作，無需樣式化後處理。
- 檔案建立無法設定父資料夾。當位置很重要時，建立
  後使用 google-drive `google_drive_move_or_rename` 搭配
  `destination_folder_id`.

<a id="structure-is-not-optional"></a>

## 結構不可或缺

沒有階層的檔案看起來像文字傾倒。任何
報告/備忘錄/筆記請求的預設結構：一個 TITLE 段落、一個斜體署名或日期行、
每個區段的 HEADING_2、簡短的正文段落，在關鍵
文字上使用粗體，以及專案符號列表用於列舉。使用 `insert_text` 的
paragraphStyle/bold/italic 引數在寫入時樣式化，而非
事後樣式化。

<a id="styling"></a>

## 樣式化

- 字元樣式（`apply_text_style`）：粗體/斜體/下劃線/刪除線、
  fontSize、fontFamily、textColor/backgroundColor 作為 `#RRGGBB`、linkUrl。
  排除性 END 索引：樣式化一個段落而不包含其尾隨換行，
  使用讀取中的 `end - 1`。
- 段落樣式：`apply_paragraph_style` 用於命名樣式
  （TITLE/HEADING_1..6），`set_paragraph_format` 用於對齊、縮排和
  行距（百分比，100 = 單倍）。
- 列表：`create_list` 覆蓋段落範圍；每個換行分隔的
  段落成為一個專案。先插入純文字行，然後轉為專案符號。
- 預設是正確性的關鍵：列舉預設使用 BULLET_* 預設；
  NUMBERED_* 僅當內容有順序（步驟、排名）或
  請求指定數字時使用。
- 在轉為專案符號之前，檢查讀取中是否已有段落顯示
  前導專案符號：直接追加在列表後的文字會繼承其
  成員資格，`create_list` 覆蓋這些段落會重新樣式化原始
  列表，繼續其編號（第二個列表顯示 4、5、6）。先
  移除：`create_list` 搭配 `remove: true` 精確覆蓋新段落，
  然後套用想要的預設。

<a id="tables-and-images"></a>

## 表格和圖片

- `insert_table` 插入一個空白網格。填充它：重新讀取（單元格出現
  並有各自的索引範圍）並 `insert_text` 從最後一個
  單元格到第一個填充，使較早的單元格索引保持有效。
- 在填充後樣式化表格，使用表格元素的
  `start` 從 `read_document`：`set_table_cell_shading` 標題行
  （選擇該行的文字顏色以配合填充），`merge_table_cells` 用於
  跨欄的標題單元格（左上內容保留，其餘
  被丟棄），`set_table_column_width` 用於固定寬度（點）。結構
  編輯（合併）會位移索引：在進一步文字編輯前重新讀取。
- `insert_image` 需要一個公開可訪問的 URL（Google 會訪問它；認證
  牆和私有伺服器會失敗），PNG/JPEG/GIF。使用 width/height 設定尺寸
  （點）（僅設定一個時保持比例）。
- `insert_page_break` 在索引處開始新頁。

<a id="headers-and-footers"></a>

## 頁首和頁尾

`set_header_footer` 在一次呼叫中設定預設頁首或頁尾：它
在檔案沒有該區段時建立，否則替換文字，
空文字清除它。文字是靜態的（API 中沒有頁號欄位）；
它適用於第一個分頁。切勿 `insert_text` 一個假頁首
到正文中：它會隨內容滾動消失。

<a id="bulk-text-ops"></a>

## 批次文字操作

`replace_text` 替換所有位置的字面字串（或指定分頁），
無需索引計算；優先用於重新命名和修正。`delete_content`
拒絕分割表格結構或 surrogate 配對的範圍；刪除一個
段落的最終換行會將其與下一段合併。

<a id="verify-your-work"></a>

## 驗證你的工作

使用 `read_document` 重新讀取結構和樣式，或 google-drive
`google_drive_read_file` 獲取整個檔案作為 Markdown，或
`google_drive_export_file`（pdf）當視覺結果很重要時。在任何
樣式化呼叫後，確認樣式化範圍精確覆蓋預期的文字
再繼續。
