# Google Calendar

Cursor 外掛，透過 Google 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Google Calendar](https://calendar.google.com)。

列出行事曆、搜尋與檢視活動、建議時間，以及建立、更新或回應會議。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Google Calendar**。
3. 點選 **安裝**，然後完成 Google 登入提示。

或在聊天中執行 `/add-plugin google-calendar`。

## MCP

```json
{
  "mcpServers": {
    "google-calendar": {
      "type": "http",
      "url": "https://calendarmcp.googleapis.com/mcp/v1"
    }
  }
}
```

驗證是針對 Google 的 OAuth 2.0。當外掛連線時，Cursor 會提示 Google 登入。

<a id="docs"></a>

## 檔案

- Google MCP 設定：https://developers.google.com/workspace/calendar/api/guides/configure-mcp-server
- Workspace MCP 概覽：https://developers.google.com/workspace/guides/configure-mcp-servers

標誌是官方 Google Calendar 產品圖示，放置在白色帶有留白的圖塊上，以便在 Cursor 介面中清晰呈現：
https://www.gstatic.com/images/branding/productlogos/calendar_2026/v1/192px.svg

<a id="license"></a>

## 授權

MIT
