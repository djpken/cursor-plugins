# Square

Cursor 外掛，透過 Square 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（由 Block 提供）將代理連線到 [Square](https://squareup.com)。

透過 Square API 查詢和管理 Square 賣家帳戶：付款、訂單、商品專案、庫存、客戶、發票等。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Square**。
3. 點選 **Install**，然後完成 Square 登入提示。

或在聊天中執行 `/add-plugin square`。

## MCP

```json
{
  "mcpServers": {
    "square": {
      "type": "http",
      "url": "https://mcp.squareup.com/mcp"
    }
  }
}
```

驗證採用 OAuth 2.1，包含 Dynamic Client Registration 和 PKCE。Cursor 會自行註冊，並在外掛連線時提示 Square 登入；不需要設定存取令牌或客戶 ID。Square 讓您僅核准您希望連線具有的許可權範圍。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

伺服器提供一個小型通用工具組，可存取完整的 Square API：

| 工具 | 用途 |
| --- | --- |
| `get_service_info` | 探索 Square 服務上可用的方法（例如 `catalog`、`orders`、`payments`） |
| `get_type_info` | 在呼叫方法之前取得其引數需求 |
| `make_api_request` | 執行 Square API 呼叫 |

透過這些工具，代理可存取付款與退款、訂單、商品與庫存、客戶與忠誠度、發票與訂閱、預約、團隊成員與時間卡、禮品卡、爭議、付款結算等，僅限於您核准的範圍。

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 遠端伺服器僅存取**生產**資料。若要測試 Square Sandbox 帳戶，請改用 Square 的本地伺服器（`npx square-mcp-server start` 搭配 `ACCESS_TOKEN` 和 `SANDBOX=true`）；請參閱 Square 的說明檔案。
- 伺服器可讀取也可寫入，包括建立訂單、付款、退款和發票，當相符的 `*_WRITE` 範圍核准時。僅核准您需要的範圍，並授予僅讀範圍，若代理不應更改賣家資料。
- Square 將 MCP 伺服器標示為 beta。
- Square 維護 MCP 客戶的允許列表，用於 OAuth 註冊。若未註冊的客戶登入被拒絕，請在 Square 開發者論壇中請求新增。

<a id="docs"></a>

## 說明檔案

- Square MCP 伺服器：https://developer.squareup.com/docs/mcp
- 來源：https://github.com/square/square-mcp-server
- OAuth 許可權參考：https://developer.squareup.com/docs/oauth-api/square-permissions
- 伺服器 URL：https://mcp.squareup.com/mcp

標誌是 Square 的官方標記，來自 `square` GitHub 組織。

<a id="license"></a>

## 授權

MIT
