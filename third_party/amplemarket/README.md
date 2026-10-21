# Amplemarket

Cursor 外掛，透過 Amplemarket 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Amplemarket](https://www.amplemarket.com)。

搜尋人員與公司、豐富聯絡人資料、管理潛在客戶列表與序列，並在已登入的 Amplemarket 帳戶中取得分析資訊。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Amplemarket**。
3. 點選 **安裝**，然後完成 Amplemarket 登入提示。

或在聊天中執行 `/add-plugin amplemarket`。

## MCP

```json
{
  "mcpServers": {
    "amplemarket": {
      "type": "http",
      "url": "https://mcp.amplemarket.com/mcp"
    }
  }
}
```

驗證方式為針對 Amplemarket 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Amplemarket 登入，不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

你需要一個有效的 Amplemarket 帳戶。每位使用者各自登入；工具呼叫會以該使用者許可權執行。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 搜尋 | 使用 Searcher 篩選器搜尋人員與公司；儲存的搜尋與人物設定 |
| 資料豐富化 | 透過電子郵件、LinkedIn 連結、域名或姓名加公司來豐富人員與公司資料 |
| 聯絡人與帳戶 | 列出與查詢聯絡人/帳戶、新增備註，並檢查排除列表 |
| 潛在客戶列表 | 建立列表、新增或移除潛在客戶，並管理自訂欄位 |
| 序列 | 列出序列、建立草稿、追加或編輯步驟，並加入或移除潛在客戶 |
| 工作流程 | 列出與檢視工作流程；管理者可透過 AI Workflows 代理建立草稿 |
| Duo、收件匣、任務 | Duo 推薦潛在客戶、Unibox 訊息串、任務，以及自然語言分析 |

託管執行環境是工具名稱與結構的來源。連線後，將 `list_tasks` 作為僅讀的冒煙測試呼叫。

<a id="notes"></a>

## 備註

- 工具呼叫會以授權連線的 Amplemarket 使用者身份執行，且不能超出該使用者許可權。
- `enrich_person` 每次資料豐富化消耗 0.5 點點數（與應用內相同）。結果會快取 24 小時。顯示電子郵件或電話會消耗額外點數。
- MCP 伺服器限制為每位使用者每分鐘 100 次請求。
- 序列工具僅建立與編輯**草稿**序列。序列為線性（無分支或 HTTP 請求步驟）。啟動、結構編輯與步驟刪除需在 Amplemarket 控制檯執行。
- `create_workflow` 僅限管理者使用（每位使用者每天 25 次）。草稿必須在 Amplemarket 中審核並啟動。
- `complete_task` 僅記錄完成狀態，不會傳送電子郵件、撥打電話或執行 LinkedIn 動作。

<a id="docs"></a>

## 檔案

- 連線 Amplemarket MCP 伺服器：https://knowledge.amplemarket.com/articles/8022685319-connecting-to-the-amplemarket-mcp-server
- 使用 MCP 建立序列：https://knowledge.amplemarket.com/articles/2128059926-building-sequences-with-amplemarket-mcp
- MCP 概覽：https://www.amplemarket.com/integrations/mcp-model-context-protocol
- 伺服器 URL：https://mcp.amplemarket.com/mcp

標誌為 Amplemarket 官方應用圖示（黑色背景上的白色 A），取自 https://brand.amplemarket.com.

<a id="license"></a>

## 授權

MIT
