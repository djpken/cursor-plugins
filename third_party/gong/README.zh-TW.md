# Gong

Cursor 外掛，透過 Gong 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Gong](https://www.gong.io)。

將帳戶摘要、交易洞察和電話簡報拉入聊天中。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Gong**。
3. 點選 **安裝**，然後設定客戶端 ID 和密碼（如下），並完成 Gong 登入提示。

或在聊天中執行 `/add-plugin gong`。

## MCP

```json
{
  "mcpServers": {
    "gong": {
      "url": "https://mcp.gong.io/mcp",
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

Gong 的 MCP 伺服器使用靜態 OAuth 客戶端憑證，加上每個使用者各自的 OAuth 登入，因此必須由管理者先註冊 Cursor，才能讓任何人連線。

1. Gong 技術管理者在 **公司設定 → 生態系統 → API → 整合** 下建立 MCP 整合，並啟用 MCP 範圍。
2. 在該整合上註冊兩個 redirect URI：
   - 桌面：`http://localhost:8787/callback`
   - Web 和 Cloud Agents：`https://www.cursor.com/agents/mcp/oauth/callback`
3. 在 **Dashboard → 外掛 → 設定** 中，從該整合設定 **Gong 客戶端 ID** 和 **Gong 客戶端密碼**。
4. 當 Cursor 提示時，完成 Gong OAuth 登入。

在團隊 marketplace 中，管理者為所有人設定一次客戶端 ID 和密碼；每位成員仍需完成各自的 Gong OAuth 登入，因此工具呼叫會以該成員的 Gong 許可權執行。

<a id="docs"></a>

## 文件

- Gong MCP 伺服器概述：https://help.gong.io/docs/about-gong-mcp-server

<a id="license"></a>

## 授權

MIT
