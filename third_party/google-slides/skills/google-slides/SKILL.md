---
name: google-slides
description: "透過 google-slides MCP 工具建立與編輯 Google Slides：投影片、卡片與欄的網格、文字框、圖形、圖片、表格、樣式、版面檢查，以及每張投影片的 PNG 渲染。適用於建立投影片、排列投影片元素、匹配模板，或確認投影片的呈現效果。"
---

<a id="google-slides-authoring"></a>

# Google Slides 製作

座標是以投影片左上角為原點的點；標準投影片為 720x405
（read_slide_layout 會報告實際尺寸）。所有元素皆以
從 `read_presentation` 取得 objectId。從最新的 `requiredRevisionId` 傳入
讀取或寫入，讓並行的編輯會使寫入失敗，而不是破壞資料。

<a id="building-a-new-deck-compose-then-look"></a>

## 建立新投影片：先組成，再檢視

將每張投影片寫成版面，而非座標。`compose_slide` 接受一個
樹狀結構（堆疊、網格、卡片、文字、圖片）或 HTML/CSS 子集，並在
一次呼叫中排版整張投影片：同層元素尺寸相等、間距一致、垂直
居中，以及對比組合由構造保證，文字可 `fit:
"shrink"`，回應會帶上每個元素的框與版面計畫的 lint 提示。每張投影片：
計畫。每個投影片：

1. 每次都用相同的 `clear: true` 呼叫 `add_slide`（色板角色
   每次都用相同的 `theme` 搭配 `compose_slide`（色板角色
   是角色而非色碼，因此一次主題變更即可重新樣式整份投影片。
   樹中的角色是角色名稱，不是色碼，因此一次主題變更即可重新設定整個投影片組的風格。
2. "shrink"`issues`size`text_overflow`overlap` 表示兩個節點
   「縮小」`, or give the node more `尺寸`; an `重疊」表示兩個節點
   每張投影片執行一次 `gap`（LARGE）並檢視：擁擠、
3. 每個投影片一次使用 `get_slide_thumbnail`（大）並檢視：擁擠、
   （先刪除舊元素，
   色板。透過重新組合投影片來修正（先刪除舊元素，
   或在全新的空白投影片上組合，然後刪除 `delete_object` 舊的）。

樹狀配方（欄根；根框是投影片減去 48 pt 邊距）：

- 標題投影片：`stack column justify:center` 搭配 `display` 文字和一個
  KPI 卡片：`h2`，接著一個 `textSecondary` 的 `align:center`s，每個有
- 一個 `h1` 標籤（`stack row size:150 gap:24`）在一個 `card` 值之上。
  文字加圖片：`caption` 的一個 `textSecondary` 文字（`kpi`、
- `stack row gap:32`）與一個 `body`（`bullets:true`）；圖片依比例適應。
  `size:{flex:1}`）和一個 `image`（`size:{flex:1}`）；圖片以符合比例顯示。
- 議程：一個 `body` 文字，以換行分隔專案，並使用 `bullets:true`。

HTML 形式，相同結果：`<section style="background:#0B2A4A; padding:48pt;
gap:24pt">` with `<h1>`, `<div style="display:flex; gap:24pt; height:150pt">`
的 `<article class="card"><span class="caption">…</span><span
class="kpi">…</span></article>`, `<ul><li>` bullets, `<img style="flex:1">`.
並指名，絕不會忽略。
編輯既有投影片：放置、檢查、修正、檢視

<a id="editing-an-existing-deck-place-inspect-fix-look"></a>

## 對於已有內容的投影片，或樹狀結構未建模的元素：

對於已有內容的投影片組，或樹狀未建模的元素：

1. `insert_grid` 適用於任何行、欄或卡片/文字/圖片網格（一次
   呼叫，對齊，相等大小）；`insert_text_box` / `insert_shape` /
   `insert_image`：每個元素的框、z 順序、文字樣式、
2. `read_slide_layout`：每個元素的框、z 順序、文字樣式、
   估計的換行高度（`textFit.fits`）和 `issues`。修正每個
   `error` 和 `warn`（`text_overflow`、`overlap`、`off_slide`、
   `low_contrast` 使用實際位於文字後方的填色命名）以及
   `info` 專案，當成本低廉時（`near_miss_alignment`、`unequal_peers`、
   `tight_margin`, `empty_placeholder`).
3. `get_slide_thumbnail` 一旦 lint 檢查乾淨後，並檢視。

<a id="grid-conventions-when-placing-by-hand"></a>

## 標題在 x 40、y 32、寬 640、高度 60-70 適用單行 28-34 pt（兩行

約需 110）；副標題在其下 16-18 pt。內容區域 x 40、y 130、
640 x 235（insert_grid 的預設）。頁尾在 y 370、12 pt。保持 40 pt 側邊
邊距，除非元素刻意全出血。流程：
邊距，除非元素刻意全出血。流程：
`insert_grid` 步驟形狀（欄 = 步驟），然後 `connect_shapes`
34 pt 粗體標題在 600 pt 框中約 32 個字元後會換行並溢位

34 pt 粗體標題在 600 pt 框中約 32 個字元後會換行並溢位
讀取中與 `textFit` 在組成回應中可預測此情況；長標題改用
24-28 pt、`issues`，或兩行並將副標題向下推。
24-28 pt、`fit: "shrink"`，或兩行並將副標題向下推。

<a id="palette-and-contrast"></a>

## 色板與對比

`compose_slide`）：背景、一個卡片/表面色、一個強調色，以及一個
`theme.palette`）：背景、一個卡片/表面顏色、一個強調色，以及一個
模板或品牌，取其顏色；否則以下組合符合 WCAG AA：
深色：背景 #0B2A4A、卡片 #1E4976、強調 #4FC3F7、標題/文字

- #FFFFFF、次要 #B8CCE0。
  淺色：背景 #FFFFFF、卡片 #F1F3F4、強調 #0B57D0（其上白字）、文字 #202124、次要 #444746。
- 文字顏色在彩色填色上不會自動合理：每個含文字的
  呼叫需針對文字後方的填色選色（卡片的，而非

投影片的）。不要在中色調上使用中灰；在白底上，次要文字為 #444746
或更深（#5F6368 是邊界，更淺則失敗）。對於模板或品牌
投影片的）。不要在中間灰上使用中間灰；在白色上，次要文字是 #444746
（它會為每個失敗組合返回一個可透過的色調或色階）；
版面讀取中的 `check_contrast` 是之後的檢查。
彩色投影片上的表格會因主題填色呈現深底深字：
`low_contrast` 每個單元格（淺填色加深字，或強調色加白字）

彩色投影片上的表格會因主題填色呈現深色對深色：
`set_table_cell_fill` 每個單元格（淺色填色 + 深色文字，或強調色 + 白色）
並使用 `set_text_style`（cellRow/cellColumn）重新設定樣式。偏好統計卡片
少量行數。
投影片與重複
而不是少量行。

<a id="slides-and-repetition"></a>

## 投影片與重複

- 資料夾，因此之後用 google-drive `create_presentation` 移動。
  `set_shape_text` 搭配預定義版面會在同一呼叫中填滿標題/正文佔位符
  （最快且主題一致的投影片）；`google_drive_move_or_rename` 重新排序。
- `add_slide` 使用預定義佈局會填入標題/正文佔位符
  投影片，然後每個變體在投影片 id 上執行 `move_slide`（副本
- 重複佈局（一個模板，N 個變體）：先完整建立第一個投影片，
  然後每個變體在投影片 id 上使用 `duplicate_object`（副本
  會緊接在後）並使用 `pageObjectIds` 範圍限定 `replace_text` 到
  副本；像 {{TITLE}} 這樣的明確佔位符讓替換無歧義。
- 單元格的全部，而非範圍。`set_shape_text` 移動但不調整大小：
  {rowIndex, columnIndex}）；`set_text_style` 重新設定一個形狀的所有樣式，或
  講者備註：`update_element_position` 搭配 pageObjectId = 投影片的
  刪除並在正確大小重新建立。
- 整份投影片渲染：google-drive `set_shape_text`（pdf）。
  notesPageId 和 objectId = 從 `read_presentation` 取得 speakerNotesObjectId。
- 整個投影片組渲染：google-drive `google_drive_export_file`（pdf）。
