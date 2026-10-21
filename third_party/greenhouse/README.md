# Greenhouse

Cursor 外掛，透過 Greenhouse 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Greenhouse](https://www.greenhouse.com) 招募系統。

搜尋候選人、職位和申請，檢視評分卡和麵試階段，並且在您的管理員允許的情況下，在已登入的 Greenhouse 組織中執行招募操作。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Greenhouse**。
3. 點選 **安裝**，然後完成 Greenhouse 登入提示。

或在聊天中執行 `/add-plugin greenhouse`。

## MCP

```json
{
  "mcpServers": {
    "greenhouse": {
      "type": "http",
      "url": "https://mcp.us.greenhouse.io/mcp"
    }
  }
}
```

認證是針對 Greenhouse 的 OAuth 2.0，使用 PKCE 和動態客戶端註冊。當外掛連線時，Cursor 會提示 Greenhouse 登入，不需要設定 API 金鑰。伺服器請求 `greenhouse:mcp_client` 範圍。

<a id="before-you-connect"></a>

## 連線前

Greenhouse **網站管理員** 必須在 **Configure → Dev Center → MCP Access** 下啟用 MCP 範圍並儲存。直到範圍儲存後，每次登入都會在 Greenhouse 的「無法授權」頁面結束。Greenhouse MCP 可在 Greenhouse Recruiting Core、Plus 和 Pro 上使用。

如果登入仍然在「無法授權」頁面結束，請要求網站管理員在 MCP Access 的 **Clients** 選項卡中新增 Cursor 的 redirect URL：

- `https://www.cursor.com/agents/mcp/oauth/callback` (Grok Bot)
- `http://localhost:8787/callback` (Grok Bot 本地 fallback)
- `cursor://anysphere.cursor-mcp/oauth/callback` (Cursor IDE)

<a id="notes"></a>

## 注意

- 工具呼叫以授權連線的 Greenhouse 使用者身份執行，受組織啟用的 MCP 範圍和該使用者 Greenhouse 許可權的限制。未設定的組織會獲得僅讀範圍。
- Greenhouse 會阻止刪除端點，並在執行高影響寫入操作（如拒絕或僱用）之前要求確認。
- Greenhouse MCP 處於 beta 階段；工具輸入和輸出可能隨時變更，恕不另行通知。

<a id="docs"></a>

## 文件

- Greenhouse MCP 概述：https://support.greenhouse.io/hc/en-us/articles/52090956642075-Greenhouse-MCP
- 設定 Greenhouse MCP 與支援的 AI 工具：https://support.greenhouse.io/hc/en-us/articles/52096319906971-Set-up-Greenhouse-MCP-with-supported-AI-tools
- 伺服器 URL：https://mcp.us.greenhouse.io/mcp

標誌是 Greenhouse 官方應用圖示（品牌綠色底上的白色「g」），來自 https://www.greenhouse.com.

<a id="license"></a>

## 許可

MIT
