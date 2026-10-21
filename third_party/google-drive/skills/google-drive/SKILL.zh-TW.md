---
name: google-drive
description: "透過其 MCP 工具與 Google Drive 協作：搜尋、讀取、建立、整理、分享、留言及匯出檔案。適用於在特定資料夾中建立檔案、移動或重新命名、將 Markdown/CSV 轉換為 Docs/Sheets、管理留言，或匯出渲染的 PDF。"
---

<a id="google-drive-files"></a>

# Google Drive 檔案

<a id="ids-come-from-search-never-guesses"></a>

## ID 來自搜尋，絕不臆測

每個工具都需要 Drive 檔案 ID。請從 `search`（或
`list_folder` 取得已知資料夾，
`list_recent_files` 取得「我最近處理的內容」），或從
使用者貼上的 URL 中的 ID。`exact_name` 勝過 `query` 對於已知的
檔案名稱；加入 `mime_type_filter` 來減少雜訊。切勿自行臆造 ID 或重用
過時對話中的 ID：請先重新搜尋。

<a id="folder-targeting"></a>

## 資料夾定位

- `create_folder` 需要 `parent_folder_id`；若未提供，
  資料夾會落在 My Drive 根目錄。當使用者指定目的地資料夾，
  請解析其 ID 並傳入：若指定資料夾卻在根目錄建立檔案，
  即使內容正確也是錯誤結果。
- `create_file` 也需要 `parent_folder_id`，因此請直接
  在正確位置建立檔案，而非建立後再移動。
- Docs/Sheets/Slides 供應商自身的建立工具無法指定資料夾
  （其 API 沒有 parent 引數）。當位置很重要時，
  請使用 `create_file` 直接在目標位置建立檔案（Markdown 轉
  Doc、CSV 轉 Sheet），或建立後再使用 `move_or_rename`
  搭配 `destination_folder_id`。
- `move_or_rename` 可在一次呼叫中執行任一或兩者；重新命名
  不會改變 ID。
- `update_file` 是相同的重新命名/移動操作，使用託管 Drive 工具的
  引數名稱（`fileId`；`title` 重新命名，`parentId` 移動並取代
  當前 parent）；它同時存在於翻譯器和託管
  介面，因此在必須適用於任一介面的指引中，請優先使用它。
- 授許可權制：此應用程式授權為 `drive.file`，因此移動或寫入
  此應用程式未曾建立（且使用者未曾選取）的檔案/資料夾可能導致 403。
  該 403 是許可權事實，而非暫時錯誤；請勿重試。

<a id="markdown-and-csv-conversion"></a>

## Markdown 與 CSV 轉換

`create_file` 搭配 `target_mime_type:
application/vnd.google-apps.document` 可在一次呼叫中將 Markdown（標題、列表、
表格）轉換為真正的 Google Doc；`...spreadsheet` 將 CSV 轉換
為 Sheet。這是產生格式化 Doc 的最快正確方式：
當內容事先已知時，請優先使用此方式而非建立後插入。
`update_file_content` 以相同方式就地轉換。

<a id="copying-metadata-and-sharing"></a>

## 複製、metadata 與分享

- `copy_file` 複製檔案（可選擇複製到資料夾）：
  這是在不修改原始檔案的情況下建立模板的正確方式。
- `get_file_metadata` 返回完整記錄（mime type、
  parents、size、owners、modified time）；請用它確認移動已完成。
- `get_file_permissions` 列出誰有存取權；
  `share_file` 授予角色給電子郵件。僅在
  使用者要求時分享；請報告連結而非臆測性地擴大存取範圍。
- `download_file` 返回 base64 編碼的原始 bytes
  適用於非 Google 檔案（圖片、PDF、純文字）。

<a id="reading-and-exporting"></a>

## 讀取與匯出

- `read_file` 返回文字：Docs 為 Markdown、Sheets 為 CSV 每個
  tab、Slides 為純文字。請用它驗證寫入後的內容。
- `export_file` 返回 base64 編碼的渲染 bytes：`pdf`
  適用於 Docs/Sheets/Slides（Sheets PDF 渲染每個 tab，包含圖表）、
  `png`/`svg` 適用於 Drawings，加上 office 格式（docx/xlsx/pptx）。當使用者想看到最終外觀時請使用 PDF；匯出上限為 10 MB。
  當使用者想要檢視最終效果時；匯出上限為 10 MB。
- 若要將單張投影片作為 PNG，請使用 google-slides 的 `get_slide_thumbnail` 工具，而非
  留言

<a id="comments"></a>

## 留言位於 Drive 檔案上，因此一套工具即可涵蓋 Docs、Sheets 和

Slides：
Slides 類似：

- 文字、已解決狀態和回覆；它會分頁，因此請先遵循 `list_comments`
  文字、已解決狀態和回覆；它會分頁，因此請遵循 `nextPageToken`
  在判定一個討論串不存在之前。
- `add_comment` 以留言 ID 回覆，其
- `reply_to_comment`（內容可省略）是唯一
  `action: "resolve"`（內容可選）是唯一能解決
  不要處理已解決的討論串。
  刪除

<a id="deleting"></a>

## 刪除

`trash_file` 是可恢復的（約 30 天），並適用於檔案和
刻意不提供；切勿承諾。
驗證你的工作

<a id="verify-your-work"></a>

## 驗證你的工作

在建立和移動之後，請用 `list_folder` 確認放置位置
在目的地或 `get_file_metadata`（檢查 `parents`）。
而 `read_file`（pdf）是渲染結果的真實來源。
以及 `export_file`（PDF）來檢視渲染結果。
