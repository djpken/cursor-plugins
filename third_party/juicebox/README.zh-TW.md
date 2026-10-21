# Juicebox

Cursor 外掛，透過 Juicebox 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Juicebox](https://juicebox.ai)。

在已登入的 Juicebox 工作區中，查詢招募分析、搜尋專案候選名單、檢視專案，以及建立或管理搜尋代理。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Juicebox**。
3. 點選 **安裝**，然後完成 Juicebox 登入提示。

或在聊天中執行 `/add-plugin juicebox`。

## MCP

```json
{
  "mcpServers": {
    "juicebox": {
      "type": "http",
      "url": "https://mcp.juicebox.ai/v1"
    }
  }
}
```

驗證方式是透過 Dynamic Client Registration (DCR) 對 Juicebox 進行 OAuth 2.0。Cursor 會自行註冊，並在外掛連線時提示 Juicebox 登入，不需要設定 API 金鑰或客戶 ID。

<a id="before-you-connect"></a>

## 連線前

Juicebox MCP 伺服器適用於所有 Juicebox 付費方案。

在設定過程中，您會看到一個核准畫面，列出 MCP 伺服器請求的許可權：讀取招募分析以及建立或調整代理。核准後即可繼續。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 分析 | 列出可用資料表與欄位 (`get_schema`)、執行僅讀報告 (`get_data`)，以及將名稱或連結解析為內部 ID |
| 候選名單 | 列出已儲存至專案候選名單的候選人，然後取得更詳細的資訊，例如年資與任期 |
| 專案 | 摘要專案資訊，包括已儲存搜尋與候選名單數量 |
| 代理 | 建立或調整搜尋代理、開始搜尋，以及暫停、恢復、結束或重新開啟代理 |

在分析查詢之前呼叫 `get_schema`，在建立代理之前呼叫 `get_agent_schema`。託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Juicebox 使用者身份執行，且不能超出該使用者許可權。使用者無法查詢在 Juicebox 中尚未擁有存取權的專案、搜尋或候選人。
- 分析與候選名單工具僅讀。代理工具可以在已登入的工作區中建立、開始、暫停、恢復、結束或重新開啟搜尋代理。
- 透過 MCP 伺服器匯出的完整候選人資料（經歷與聯絡資訊）會計入方案每月匯出上限。輕量查詢，例如候選人姓名或 LinkedIn URL，則不會計入。

<a id="docs"></a>

## 檔案

- Juicebox MCP: https://docs.juicebox.ai/juicebox-mcp
- 伺服器 URL: https://mcp.juicebox.ai/v1

標誌是 Juicebox 官方 apple-touch 圖示（品牌紫色底上的白色果汁盒圖形），取自 https://juicebox.ai.

<a id="license"></a>

## 授權

MIT
