# Customer.io

Cursor 外掛，透過 Customer.io 官方遠端 [Model Context Protocol](https://customer.io) 伺服器，將代理連線到 [Customer.io](https://modelcontextprotocol.io/)。

檢視工作區資料、建立和管理活動與一次性傳送、處理區段和人群，以及在已登入的 Customer.io 工作區中查詢 Journeys 和 Data Pipelines API。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Customer.io**。
3. 點選 **Install**，然後完成 Customer.io 登入提示。

或在聊天中執行 `/add-plugin customer-io`。

## MCP

```json
{
  "mcpServers": {
    "customer-io": {
      "type": "http",
      "url": "https://mcp.customer.io/mcp"
    }
  }
}
```

驗證採用 OAuth 2.1 搭配 PKCE 和 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Customer.io 登入，無需設定 API 金鑰或客戶 ID。在流程中，您可以選擇授予的工作區和許可權範圍。

<a id="before-you-connect"></a>

## 連線前

Customer.io 帳戶管理員必須先在 **Settings → AI** 中開啟 MCP，包括編輯真實資料和讀取敏感資料的獨立開關。在此之前，連線會在驗證階段失敗。

此外掛指向 **US** 區域。如果您的帳戶位於 EU，請將伺服器 URL 改為 `https://mcp-eu.customer.io/mcp`。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 工作區環境 | 先提供代理工作區結構，然後瀏覽物件和屬性結構 |
| 人群與物件 | 查詢人群、物件及其屬性、關係和活動 |
| 區段 | 建立、檢視和查詢區段及其成員 |
| 活動與傳送 | 建立和管理活動、電子報和一次性傳送 |
| 資料管道 | 查詢 CDP 來源、目的地和傳送資料 |

託管執行環境是工具名稱和結構的唯一來源。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Customer.io 使用者身份執行，並僅限於 OAuth 期間授予的工作區。
- 工具表面刻意保持簡潔：並非每個物件一個工具，伺服器提供環境說明、結構瀏器和以動詞為範圍的 API 代理，將讀取與寫入和刪除分開，以便讀取工具可自動核准。
- 設定傳輸型別為 `http`。Customer.io 不支援 `sse` 傳輸。
- EU 區域帳戶必須使用 `https://mcp-eu.customer.io/mcp`；美國 URL 不會提供 EU 資料。

<a id="docs"></a>

## 文件

- Customer.io MCP: https://docs.customer.io/ai/mcp/
- 連線 IDE: https://docs.customer.io/ai/mcp/ide/
- 伺服器 URL: https://mcp.customer.io/mcp

標誌是 Customer.io 官方標識，來自 `customerio` GitHub 組織。

<a id="license"></a>

## 授權

MIT
