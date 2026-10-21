# Docs Canvas

Cursor 外掛程式，用於將檔案（包括架構說明、API 參考、操作手冊及程式碼庫導覽）呈現為可導覽的 Cursor Canvas，而非平坦的 markdown 檔案。

<a id="status"></a>

## 狀態

此外掛程式為**初始骨架**。skill 結構已完整，Canvas 歡迎頁面會在市集（marketplace）中顯示它，但 skill 主體刻意設計為起始大綱，而非完全調校的 playbook。隨著「檔案畫布」模式的成熟，預期會持續迭代。

<a id="what-it-includes"></a>

## 包含內容

- `docs-canvas`：建立一個 Canvas，以概覽卡片、可導覽的目錄、混合文字/程式碼/圖表的章節，以及參考區塊來呈現結構化檔案。

<a id="when-to-use"></a>

## 使用時機

- 將架構說明、設計檔案或 RFC 呈現為可快速瀏覽的內容，而非僅限於從上到下閱讀。
- 將一個 markdown 檔案目錄或單一大型檔案轉換為具有跳轉導覽功能的 Canvas。
- 以比單一回覆更豐富的版面設定（章節、圖表、表格、提示框）來回答程式碼庫相關問題。

使用「docs canvas」、「documentation overview」、「architecture walkthrough」、「API reference page」或「render this doc as an interactive canvas」等短語觸發。

<a id="how-its-organized"></a>

## 組織方式

此 skill 預期 docs canvas 應以以下內容開頭：

1. **概覽**：簡短摘要卡片，說明目的、範圍與目標受眾。
2. **目錄**：固定或釘選的章節清單，方便讀者跳轉。
3. **主體章節**：每個邏輯單元（架構、API、範例、注意事項）各佔一章，內容混合文字、程式碼區塊、圖表及提示框。
4. **參考資料**：連結至相關檔案、原始檔案、RFC 及外部資料。

這些是基準而非上限，此 skill 鼓勵針對特定主題，採用任何實際有助於讀者的呈現方式（圖表、表格、決策樹、實作範例）。

<a id="requirements"></a>

## 需求

- 已啟用 Canvas 的 Cursor。
- 原始資料：markdown 檔案目錄、單一檔案 URL、內嵌大綱，或需回答的程式碼庫問題。

<a id="license"></a>

## 授權條款

MIT
