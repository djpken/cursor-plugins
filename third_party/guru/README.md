# Guru

Cursor 外掛，透過 Guru 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Guru](https://www.getguru.com)。

針對公司的 Guru 知識庫及連線來源提出問題，取得具許可權感知且附有引文的答案，並起草或更新 Guru Cards。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Guru**。
3. 點選 **Install**，然後完成 Guru 登入提示。

或在聊天中執行 `/add-plugin guru`。

## MCP

```json
{
  "mcpServers": {
    "guru": {
      "type": "http",
      "url": "https://mcp.api.getguru.com/mcp"
    }
  }
}
```

驗證採用 OAuth 2.0。當外掛連線時，Cursor 會提示 Guru 登入。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 搜尋 | 搜尋 Guru Cards 及連線來源，例如 Salesforce、Slack、Google Drive、Confluence 和 SharePoint |
| 驗證答案 | 透過 Knowledge Agents 產生答案，並附上來源引文 |
| Cards | 建立和更新 Guru Cards |

託管執行環境是工具名稱和結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Guru 使用者身份執行，並尊重該使用者內容的許可權。
- Guru 也接受 API 令牌，但採用特殊的組合形式：`Authorization: Bearer [EMAIL]:[TOKEN]`，其中電子郵件為 Guru 帳戶電子郵件，令牌來自 **Admin → API Tokens**。
- Guru 僅支援 HTTP 串流，不支援伺服器傳送事件。
- 所有查詢都會記錄在 Guru 的 AI Agent Center。

<a id="docs"></a>

## 檔案

- Guru MCP 伺服器概覽：https://developer.getguru.com/docs/guru-mcp-server-overview
- 驗證與連線設定：https://developer.getguru.com/docs/authentication-connection-setup
- 連線 Guru 的 MCP 伺服器：https://help.getguru.com/docs/connecting-gurus-mcp-server
- 伺服器 URL：https://mcp.api.getguru.com/mcp

標誌為 Guru 官方標記，來自 `guruhq` GitHub 組織。

<a id="license"></a>

## 授權

MIT
