# Jobber

Cursor 外掛，透過 Jobber 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Jobber](https://www.getjobber.com/)。

在已登入的 Jobber 帳戶中，查詢和管理客戶、請求與報價，並讀取工作、訪視與帳單資訊。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Jobber**。
3. 點選 **安裝**，然後設定 Client ID 與 Client Secret（如下），並完成 Jobber 登入提示。

或在聊天中執行 `/add-plugin jobber`。

## MCP

```json
{
  "mcpServers": {
    "jobber": {
      "type": "http",
      "url": "https://mcp.getjobber.com/mcp",
      "auth": {
        "CLIENT_ID": "${CLIENT_ID}",
        "CLIENT_SECRET": "${CLIENT_SECRET}"
      }
    }
  }
}
```

<a id="setup"></a>

## 設定

Jobber 的 OAuth 伺服器不支援動態客戶註冊，並以金鑰驗證客戶，因此必須先註冊一個 Jobber 應用程式，才能讓任何人連線。

1. 登入 [Jobber Developer Center](https://developer.getjobber.com/) 並建立一個應用程式。
2. 在該應用程式上註冊兩個 redirect URI：
   - 桌面版：`http://localhost:8787/callback`
   - Web 與雲端代理：`https://www.cursor.com/agents/mcp/oauth/callback`
3. 授予連線所需的 MCP 許可權範圍（如下所列），然後複製應用程式的 **Client ID** 與 **Client Secret**。
4. 在 **Dashboard → Plugins → Configure** 中，從該應用程式設定 **Jobber Client ID** 與 **Jobber Client Secret**。
5. 當 Cursor 提示時，完成 Jobber OAuth 登入。

在團隊市場中，管理員可以為所有人設定一次憑證；每位成員仍需完成各自的 Jobber OAuth 登入，因此工具呼叫會以該成員的 Jobber 許可權執行。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 區域 | 許可權範圍 |
| --- | --- |
| 客戶 | `mcp:read_clients`, `mcp:write_clients` |
| 請求 | `mcp:read_requests`, `mcp:write_requests` |
| 報價 | `mcp:read_quotes`, `mcp:write_quotes` |
| 工作 | `mcp:read_jobs` |
| 帳單 | `mcp:read_invoices` |
| 訪視 | `mcp:read_visits` |

工作、帳單與訪視僅可讀取。託管執行環境是工具名稱與結構的來源基準。

<a id="docs"></a>

## 文件

- Jobber：https://www.getjobber.com/
- Jobber Developer Center：https://developer.getjobber.com/
- 伺服器 URL：https://mcp.getjobber.com/mcp

Logo 是 Jobber 的官方標誌，由 Jobber 提供。

<a id="license"></a>

## 授權

MIT
