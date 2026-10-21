# Docusign

Cursor 外掛，透過 Docusign 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（beta）將代理連線到 [Docusign](https://www.docusign.com)。

處理電子簽章信封與模板、Maestro 工作流程，以及從已登入的 Docusign 帳戶中取得 Navigator 協議資料。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Docusign**。
3. 點選 **安裝**，然後設定 Integration Key 與 Secret Key（如下），並完成 Docusign 登入提示。

或在聊天中執行 `/add-plugin docusign`。

## MCP

```json
{
  "mcpServers": {
    "docusign": {
      "type": "http",
      "url": "https://mcp.docusign.com/mcp",
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

Docusign MCP 需要一個保密 OAuth 應用（Authorization Code Grant）。在任何人可以連線之前，必須先建立 Integration Key。

1. 登入您的 [Docusign 帳戶](https://www.docusign.com/)（或 [開發者帳戶](https://developers.docusign.com/)），並開啟 **設定 → Apps and Keys**。
2. 新增一個應用，複製 **Integration Key**，並產生 **Secret Key**。
3. 在該應用上註冊兩個 redirect URI：
   - 桌面：`http://localhost:8787/callback`
   - Web 與 Cloud Agents：`https://www.cursor.com/agents/mcp/oauth/callback`
4. 在 **Dashboard → Plugins → Configure** 中，從該應用設定 **Docusign Integration Key** 與 **Docusign Secret Key**。
5. 當 Cursor 提示時，完成 Docusign OAuth 登入。

在團隊 marketplace 中，管理員可以為所有人設定一次憑證；每個成員仍需完成自己的 Docusign OAuth 登入，因此工具呼叫會以該成員的許可權執行。

<a id="demo-vs-production"></a>

## Demo 與 production

此外掛指向 production MCP URL。對於開發者/demo 帳戶，安裝後將 `mcp.json` 中的 `url` 改成 `https://mcp-d.docusign.com/mcp`。

| 環境 | URL |
| --- | --- |
| Production（預設） | `https://mcp.docusign.com/mcp` |
| Demo（開發者帳戶） | `https://mcp-d.docusign.com/mcp` |

<a id="notes"></a>

## 注意事項

- MCP 伺服器目前為 beta。隨著 Docusign 新增工具並調整介面，預期會有變動。
- 僅支援 **Confidential Authorization Code Grant** 令牌。不支援 JWT、Implicit 或 Public Authorization Code Grant。

<a id="docs"></a>

## 文件

- Docusign MCP 伺服器（beta）：https://developers.docusign.com/platform/mcp-server/
- Confidential Authorization Code Grant：https://developers.docusign.com/platform/auth/confidential-authcode-get-token

Logo 是 Docusign 開發者中心的應用圖示（192×192），來自 https://developers.docusign.com/.

<a id="license"></a>

## 授權

MIT
