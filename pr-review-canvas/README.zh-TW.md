<a id="pr-review-canvas"></a>

# PR 審查畫布

Cursor 外掛程式，用於將 Pull Request 差異呈現為互動式 Cursor Canvas，其組織方式旨在協助審查者理解，而非依檔案樹順序排列。

<a id="what-it-includes"></a>

## 包含內容

- `pr-review-canvas`：建立一個 Canvas，將 PR 差異呈現為頂部顯示核心邏輯、中部簡化顯示接線與整合、底部摘要顯示樣板程式碼；針對真正複雜的部分，加入偽程式碼、範例追蹤與內嵌提示。

<a id="when-to-use"></a>

## 使用時機

- 審查 Pull Request 時，希望獲得能突顯真正風險的逐步說明，而非逐檔案的差異檢視。
- 為人類審查者摘要一疊 PR。
- 要求差異逐步說明、變更集總覽，或「這裡實際變更了什麼」的檢視。

使用類似「PR 審查畫布」、「在畫布上審查這個 PR」、「在畫布上帶我走過這個差異」等短語觸發，或將 agent 指向 PR URL / 分支參考。

<a id="how-its-organized"></a>

## 組織方式

此 skill 依審查價值對變更進行分組：

1. **核心邏輯**：新行為、演演算法變更、狀態轉換、API 介面變更。包含完整差異與上下文。
2. **接線與整合**：路由註冊、DI、設定管線。簡化呈現。
3. **樣板與機械性變更**：匯入、重新命名、產生的程式碼、格式調整。以清單形式摘要，除非直接相關，否則不顯示內嵌差異。

接著，針對密集的邏輯加入偽程式碼，針對難以從差異預測的行為變更加入具體的前後範例追蹤，並針對真正令人驚訝或具風險的區塊加入內嵌提示（`Subtle`、`Breaking`、`Race condition`、`Perf`）。

<a id="requirements"></a>

## 需求

- 已啟用 Canvas 的 Cursor。
- 可存取差異來源：本地分支/參考（`git diff`）、GitHub PR URL 或編號（`gh pr diff`），或 Graphite 堆疊（`gt` CLI / `gh`）。

<a id="license"></a>

## 授權條款

MIT
