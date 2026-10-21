# Zoom

Cursor 外掛，透過 Zoom 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Zoom](https://zoom.us)。

從聊天中搜尋會議和錄影、提取摘要和逐字稿，以及處理 Zoom Docs。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Zoom**。
3. 點選 **安裝**，然後設定所需的 Client ID 和 Client Secret，或使用 Public Client ID（如下），並完成 Zoom 登入提示。

或在聊天中執行 `/add-plugin zoom`。

## MCP

```json
{
  "mcpServers": {
    "zoom": {
      "type": "http",
      "url": "https://mcp.zoom.us/mcp/zoom/streamable",
      "auth": {
        "CLIENT_ID": "${CLIENT_ID}",
        "CLIENT_SECRET": "${CLIENT_SECRET:-}"
      }
    }
  }
}
```

<a id="setup"></a>

## 設定

Zoom 的 MCP 伺服器僅支援手動客戶註冊：不接受 Dynamic Client Registration 和 Client ID Metadata Documents，因此必須由管理員先將 Cursor 註冊為 Zoom 應用程式，才能讓任何人連線。

1. Zoom 管理員或開發者登入 [Zoom App Marketplace](https://marketplace.zoom.us)，並在 **Develop → Build app** 下建立 **General app**。
2. 在 [Zoom 的 MCP 伺服器文件](https://developers.zoom.us/docs/mcp/servers/) 中，為每個工具新增所列的 scopes。會議搜尋和錄影需要 `ai_companion:read:search` 才能進行跨 Zoom 搜尋。
3. 在 **Basic Information → OAuth Information** 下，註冊兩個 redirect URI：
   - 桌面：`http://127.0.0.1:8787/callback`
   - Web 和 Cloud Agents：`https://www.cursor.com/agents/mcp/oauth/callback`
4. 在 **Basic Information → App Credentials** 下，複製 **Client ID** 和 **Client Secret**。Client Secret 必須與 Client ID 一起使用。若要無需 secret 登入，請開啟 **Use Public Client OAuth** 並複製 **Public Client ID**。
5. 在 **Dashboard → Plugins → Configure** 中，將 **Zoom Client ID or Public Client ID** 和 **Zoom Client Secret** 設定為您複製的值。僅在使用 Public Client ID 時，才將 secret 留空。
6. 當 Cursor 提示時，完成 Zoom OAuth 登入。

如果您在 Zoom 中重新生成 Client Secret，也需要在 Cursor 中輸入新的值。透過 loopback redirect 的桌面登入需要 Public Client ID：Zoom 僅接受 `http://127.0.0.1:8787/callback` 作為 public client，並直接拒絕 `localhost`。

每位成員都需要擁有他們想要使用的 Zoom 產品授權。在團隊 marketplace 中，管理員為所有人設定一次憑證；每位成員仍需完成自己的 Zoom OAuth 登入，因此工具呼叫會以該成員的 Zoom 許可權執行。

<a id="other-zoom-mcp-servers"></a>

## 其他 Zoom MCP 伺服器

Zoom 將工具分散到產品特定的伺服器。此外掛提供主要的 `zoom` 伺服器，涵蓋會議搜尋、跨 Zoom 搜尋、錄影、摘要、會議資產，以及主伺服器的 Zoom Docs 工具。若要使用其他伺服器，請將其新增到 `mcp.json` 中，使用相同的 `auth` 區塊：

| 伺服器 | URL |
| --- | --- |
| Zoom Docs | `https://mcp.zoom.us/mcp/docs/streamable` |
| Whiteboard | `https://mcp.zoom.us/mcp/whiteboard/streamable` |
| Team Chat | `https://mcp.zoom.us/mcp/team_chat/streamable` |

Zoom 也在相同路徑上提供 SSE 變體，使用 `/sse` 而非 `/streamable`。

<a id="docs"></a>

## 文件

- Zoom MCP 概述：https://developers.zoom.us/docs/mcp/servers/
- 連線 MCP 客戶端：https://developers.zoom.us/docs/mcp/servers/connect-to-zoom-mcp-servers/

<a id="license"></a>

## 授權

MIT
