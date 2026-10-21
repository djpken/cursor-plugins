---
name: origin-api
description: >-
  將關於 Cursor Origin API 的問題路由到
  Origin 檔案中的正確章節，並列出應優先檢查的少數規則。當任務提及
  Origin、Origin API、Origin Apps 或 Origin webhooks 時使用，包括建立
  Origin App、以 Origin App 身分進行驗證、呼叫 Origin 端點，或處理
  Origin webhook 傳遞。
license: MIT
compatibility: >-
  Needs network access to https://cursor.com/docs/api/origin/* at run time.
---

# Origin API

檔案是事實來源。不要憑記憶指定端點、範圍、事件 slug、
標頭或限制。當此檔案與檔案不一致時，以
檔案為準。

檔案位於 `https://cursor.com/docs/api/origin/` 之下。`llms.txt` 是
索引，連結每個章節、端點和 webhook 酬載。
`openapi.yaml` 是合約，而「Endpoint reference」說明其
`x-origin-*` 延伸。`llms-full.txt` 是單一檔案中的完整參考。
`changelog` 說明哪些專案已移動。對於單一問題，從 `llms.txt` 開始，
僅擷取能回答該問題的章節。`llms-full.txt` 和 `openapi.yaml`
各自有數百 KB 大小，過大無法完整讀入上下文。
當您需要全部內容時（例如移植簡報），如果
可以，請將它們儲存在本地，位於您正在工作的任何儲存庫之外，搜尋您需要的章節
標題或註解，並僅讀取相符的部分。引用
`operationId` 和章節名稱。

<a id="rules-to-check-first"></a>

## 應優先檢查的規則

1. **原生或映象。** 儲存庫要麼是原生（在 Origin 上建立），
   要麼是 GitHub 映象。在原生儲存庫上，安裝具有完整的
   範圍和所有寫入許可權。在 GitHub 映象上，除了
   `repository.pushed` 之外的所有事件仍會到達，所有超出元資料和
   內容讀取的呼叫都會返回 `403`，且合併 pull request 或變更
   預設分支不可用（「Mirrored repositories」、「Events」）。
2. **訂閱。** 只有 `installation.*` 事件在沒有訂閱的情況下到達。
   缺少訂閱會導致靜默，而不是錯誤（「Events」）。
3. **驗證、去重、確認。** 在解析之前，對原始主體進行簽名
   驗證，根據傳遞 ID 去重，返回 `2xx`，然後處理
   （「Signature verification」、「Retries」、「Automatic disable」）。Origin 對
   摘要進行簽名，而 Standard Webhooks 不會，因此通用驗證器會失敗。
4. **來自規範的範圍。** 請求應用程式所呼叫操作之 `x-origin-scopes.scopes`
   的聯集（「Scopes」）。
5. **不透明令牌和 ID。** 不要建立或解析頁面令牌或 ID
   （「Pagination」、「IDs」）。

索引中未明顯顯示的一個章節名稱：如果
check-run post 以錯誤順序到達，會發生什麼情況，位於「Ordering writes」之下。

移植現有的 GitHub App：在此使用 `port-github-app-to-origin`
plugin。
