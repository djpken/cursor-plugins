# Gmail

Cursor 外掛，透過 Google 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Gmail](https://mail.google.com)。

搜尋郵件串、閱讀訊息、管理標籤與草稿，以及在已登入的 Gmail 帳戶中撰寫郵件。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Gmail**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin gmail`。

## MCP

```json
{
  "mcpServers": {
    "gmail": {
      "type": "http",
      "url": "https://gmailmcp.googleapis.com/mcp/v1"
    }
  }
}
```

驗證方式是針對 Google 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Google 登入。

<a id="docs"></a>

## 檔案

- Google MCP 設定：https://developers.google.com/workspace/gmail/api/guides/configure-mcp-server
- Workspace MCP 概覽：https://developers.google.com/workspace/guides/configure-mcp-servers

標誌是官方 Gmail 產品圖示，放置在帶有留白的白色底板上，以便在 Cursor 介面中呈現良好：
https://www.gstatic.com/images/branding/productlogos/gmail_2026/v1/192px.svg

<a id="license"></a>

## 授權

MIT
