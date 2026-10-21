# Smartsheet

Cursor 外掛，透過 Smartsheet 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Smartsheet](https://www.smartsheet.com)。

在您的 Smartsheet 帳戶中尋找工作表、讀取和更新行與欄，以及處理討論和工作區。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Smartsheet**。
3. 點選 **安裝**，然後設定您的 Smartsheet API 令牌（如下）。

或在聊天中執行 `/add-plugin smartsheet`。

## MCP

```json
{
  "mcpServers": {
    "smartsheet": {
      "type": "http",
      "url": "https://mcp.smartsheet.com",
      "headers": {
        "Authorization": "Bearer ${SMARTSHEET_API_TOKEN}"
      }
    }
  }
}
```

驗證方式是 Smartsheet **API 存取令牌**，以 bearer 令牌傳送。在 **帳戶 → 個人設定 → API 存取** 下產生一個，然後在 **Dashboard → 外掛 → 設定** 中設定。

<a id="before-you-connect"></a>

## 在連線之前

Smartsheet MCP 需要 **Business**、**Enterprise** 或 **Advanced Work Management** 方案。

此外掛指向美國區域。對於其他區域，請將伺服器 URL 更改為 `https://mcp.smartsheet.eu`（歐洲）或 `https://mcp.smartsheet.au`（澳洲）。

<a id="what-agents-can-do"></a>

## 代理可以執行的工作

| 分類 | 功能 |
| --- | --- |
| 探索 | 在整個帳戶中搜尋工作表、報告和 Dashboard |
| 工作表 | 讀取工作表內容並建立新工作表 |
| 行與欄 | 新增、更新和刪除行與欄 |
| 協作 | 討論、留言和附件 |
| 工作區與方案 | 工作區管理以及席位和授權使用情況 |

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以 API 令牌擁有者的許可權執行。
- Smartsheet 自身的 Cursor 指令會透過 `npx mcp-remote` 傳送。這裡不需要這樣處理，Cursor 原生支援 streamable HTTP，並直接傳送 `Authorization` 標頭。
- GitHub 上的 `smartsheet-platform/smar-mcp` 是已棄用的本地伺服器，聚合器仍會列出。此外掛使用 Smartsheet 目前支援的託管伺服器。

<a id="docs"></a>

## 文件

- Smartsheet MCP 伺服器：https://developers.smartsheet.com/ai-mcp/smartsheet/mcp-server
- 安裝 Smartsheet MCP 伺服器：https://developers.smartsheet.com/ai-mcp/smartsheet/mcp-server/install-the-smartsheet-mcp-server
- Smartsheet MCP 伺服器（協助中心）：https://help.smartsheet.com/articles/2483670-smartsheet-mcp-server
- 伺服器 URL：https://mcp.smartsheet.com

Logo 是 Smartsheet 的官方標誌，來自 `smartsheet` GitHub 組織。

<a id="license"></a>

## 授權

MIT
