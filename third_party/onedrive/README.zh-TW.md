# OneDrive

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Microsoft OneDrive](https://onedrive.com)。

瀏覽資料夾、搜尋檔案，並在已登入的 Microsoft 帳戶中讀取檔案元資料與內容。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **OneDrive**。
3. 點選 **安裝**，然後完成 Microsoft 登入提示。

或在聊天中執行 `/add-plugin onedrive`。

## MCP

```json
{
  "mcpServers": {
    "onedrive": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/onedrive/mcp"
    }
  }
}
```

驗證方式是針對 Microsoft（Entra ID）的 OAuth 2.0。當外掛連線時，Cursor 會提示 Microsoft 登入。

<a id="docs"></a>

## 檔案

- OneDrive API（Microsoft Graph）：https://learn.microsoft.com/en-us/graph/api/resources/onedrive
- Microsoft Graph 概覽：https://learn.microsoft.com/en-us/graph/overview

標誌是 Microsoft 官方 OneDrive 產品圖示，取自 Microsoft 的 Fluent 品牌圖示 CDN，放置在白色底板上並留有邊距，以便在 Cursor 介面中呈現良好：
https://res-1.cdn.office.net/files/fabric-cdn-prod_20240411.001/assets/brand-icons/product/svg/onedrive_48x1.svg

<a id="license"></a>

## 授權

MIT
