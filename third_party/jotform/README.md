# Jotform

Cursor 外掛，透過 Jotform 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Jotform](https://www.jotform.com)。

在已登入的 Jotform 帳戶中，列出與建立表格、編輯現有表格、提交表格，以及讀取提交內容。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Jotform**。
3. 點選 **安裝**，然後完成 Jotform 登入提示。

或在聊天中執行 `/add-plugin jotform`。

## MCP

```json
{
  "mcpServers": {
    "jotform": {
      "type": "http",
      "url": "https://mcp.jotform.com"
    }
  }
}
```

驗證採用 OAuth 2.0，首次連線時必須進行驗證。外掛連線時，Cursor 會提示 Jotform 登入。不支援 Bearer 令牌存取。

<a id="before-you-connect"></a>

## 連線前

只有**工作區管理員**可以安裝 Jotform MCP 應用程式。管理員核准後，每位使用者仍需各自授權。之後可從**帳戶 → 已連線的應用 → Jotform MCP → 客戶端**撤銷客戶端。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 表格 | 列出表格、建立新表格、編輯現有表格，並指派表格 |
| 提交 | 讀取提交內容並建立新的提交 |

託管執行環境是工具名稱與結構的來源。連線後，執行 `list_forms` 作為僅讀的冒煙測試。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Jotform 使用者身份執行。
- 伺服器 URL 是純主機 `https://mcp.jotform.com`，不含 `/mcp` 路徑字尾。
- 免費與付費 Jotform 帳戶均可使用。企業方案享有更高的速率限制（每分鐘 600 次請求）、自定義品牌與 SSO。
- 不提供自行託管，託管端點是唯一選項。

<a id="docs"></a>

## 文件

- Jotform MCP: https://www.jotform.com/mcp/
- 開發者頁面: https://www.jotform.com/developers/mcp/
- 伺服器儲存庫: https://github.com/jotform/mcp-server
- 伺服器 URL: https://mcp.jotform.com

標誌是 Jotform 官方標識，取自 `jotform` GitHub 組織。

<a id="license"></a>

## 授權

MIT
