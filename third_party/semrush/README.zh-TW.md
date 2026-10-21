# Semrush

Cursor 外掛，透過 Semrush 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Semrush](https://www.semrush.com)。

將 Semrush 的關鍵字、反向連結、流量和競爭情報資料直接拉入編輯器，不必離開你的工作環境。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Semrush**。
3. 點選 **安裝**，然後完成 Semrush 登入提示。

或在聊天中執行 `/add-plugin semrush`。

## MCP

```json
{
  "mcpServers": {
    "semrush": {
      "type": "http",
      "url": "https://mcp.semrush.com/v2/mcp"
    }
  }
}
```

驗證採用 OAuth 2.1，且客戶端會自行註冊，因此當外掛連線時，Cursor 只會提示 Semrush 登入，不需要設定客戶端 ID。

<a id="before-you-connect"></a>

## 連線前

你需要一個包含 API 單元的 Semrush 方案。MCP 呼叫會消耗與 REST API 相同的 API 單元餘額，而 Trends 資料則額外依賴你的 Trends 訂閱等級。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 關鍵字 | 關鍵字概覽、難度、相關詞彙，以及各資料庫的搜尋量 |
| 域名 | 域名概覽、自然與付費競爭對手，以及流量估計 |
| 反向連結 | 反向連結檔案、來源域名，以及錨點分析 |
| Trends | 流量與市場資料，依你的 Trends 訂閱等級限制 |
| 專案 | 對 Projects API v3 的唯讀存取 |

託管執行環境是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- 存取為唯讀，伺服器不會寫回 Semrush。
- Streamable HTTP 是唯一支援的傳輸方式。
- 每次呼叫都會消耗帳戶餘額中的 API 單元，因此請優先使用特定查詢，而非廣泛爬取。
- `https://mcp.semrush.com/v1/mcp` 是一個過時的 URL，仍在聚合網站上流通。目前的端點是 `/v2/mcp`。

<a id="docs"></a>

## 檔案

- Semrush MCP: https://developer.semrush.com/api/v4/introduction/semrush-mcp/
- 伺服器 URL: https://mcp.semrush.com/v2/mcp

標誌是 Semrush 的官方標記，來自 `semrush` GitHub 組織。

<a id="license"></a>

## 授權

MIT
