# Teams

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Microsoft Teams](https://www.microsoft.com/microsoft-teams)。

搜尋聊天和頻道訊息、閱讀對話、傳送訊息，以及在已登入的 Microsoft 帳戶中回應。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Teams**。
3. 點選 **安裝**，然後完成 Microsoft 登入提示。

或在聊天中執行 `/add-plugin teams`。

## MCP

```json
{
  "mcpServers": {
    "teams": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/teams/mcp"
    }
  }
}
```

驗證是針對 Microsoft（Entra ID）的 OAuth 2.0。當外掛連線時，Cursor 會提示 Microsoft 登入。需要工作或學校帳戶。讀取頻道訊息需要租戶管理員同意，才能讓外掛連線。

## Docs

- Teams API（Microsoft Graph）：https://learn.microsoft.com/en-us/graph/api/resources/teams-api-overview
- Microsoft Graph 概覽：https://learn.microsoft.com/en-us/graph/overview

標誌是 Microsoft Teams 官方產品圖示，取自 Microsoft Fluent 品牌圖示 CDN，放置在白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現：
https://res-1.cdn.office.net/files/fabric-cdn-prod_20240411.001/assets/brand-icons/product/svg/teams_48x1.svg

## License

MIT
