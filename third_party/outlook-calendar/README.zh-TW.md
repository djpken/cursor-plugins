<a id="outlook-calendar"></a>

# Outlook 行事曆

Cursor 外掛，透過 Cursor 的遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Outlook Calendar](https://outlook.com/calendar)。

列出即將到的事件、檢查日程，以及在已登入的 Microsoft 帳戶中建立、更新或取消會議。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Outlook 行事曆**。
3. 點選 **安裝**，然後完成 Microsoft 登入提示。

或在聊天中執行 `/add-plugin outlook-calendar`。

## MCP

```json
{
  "mcpServers": {
    "outlook-calendar": {
      "type": "http",
      "url": "https://api.cursor.com/rest-mcp/outlook-calendar/mcp"
    }
  }
}
```

驗證是針對 Microsoft（Entra ID）的 OAuth 2.0。當外掛連線時，Cursor 會提示 Microsoft 登入。

<a id="docs"></a>

## 檔案

- Outlook 行事曆 API（Microsoft Graph）：https://learn.microsoft.com/en-us/graph/api/resources/calendar
- Microsoft Graph 概覽：https://learn.microsoft.com/en-us/graph/overview

標誌是 Microsoft 官方 Outlook 行事曆產品圖示（`OutlookCalendar_24x`），放置在白色底板上並留有邊距，以便在 Cursor 介面中清晰呈現。Microsoft 的 Fluent 品牌圖示 CDN（`res-1.cdn.office.net/files/fabric-cdn-prod_*/assets/brand-icons/product/svg/`）沒有提供獨立行事曆圖示，因此這裡使用 Microsoft 產品圖示集中的 Outlook 行事曆圖示。

<a id="license"></a>

## 授權

MIT
