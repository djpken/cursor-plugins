# Outlook

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Microsoft Outlook](https://outlook.com)。

搜尋郵件資料夾、閱讀訊息、傳送電子郵件，並在已登入的 Microsoft 帳戶中查詢聯絡人。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Outlook**。
3. 點選 **安裝**，然後完成 Microsoft 登入提示。

或在聊天中執行 `/add-plugin outlook`。

## MCP

```json
{
  "mcpServers": {
    "outlook": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/outlook/mcp"
    }
  }
}
```

驗證方式為針對 Microsoft（Entra ID）的 OAuth 2.0。當外掛連線時，Cursor 會提示 Microsoft 登入。

<a id="docs"></a>

## 文件

- Outlook 郵件 API（Microsoft Graph）：https://learn.microsoft.com/en-us/graph/api/resources/mail-api-overview
- Microsoft Graph 概覽：https://learn.microsoft.com/en-us/graph/overview

標誌是 Microsoft 官方 Outlook 產品圖示，取自 Microsoft Fluent 品牌圖示 CDN，放置在白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現：
https://res-1.cdn.office.net/files/fabric-cdn-prod_20240411.001/assets/brand-icons/product/svg/outlook_48x1.svg

<a id="license"></a>

## 授權

MIT
