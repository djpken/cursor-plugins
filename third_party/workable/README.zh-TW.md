# Workable

Cursor 外掛，透過 Workable 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Workable](https://www.workable.com)。

在已登入的 Workable 帳戶中搜尋和推進候選人、管理職位、offer 和 requisition，並處理員工、休假和績效記錄。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor Settings → Plugins**。
2. 搜尋 **Workable**。
3. 點選 **Install**，然後完成 Workable 登入提示。

或在聊天中執行 `/add-plugin workable`。

## MCP

```json
{
  "mcpServers": {
    "workable": {
      "type": "http",
      "url": "https://mcp.workable.com/mcp"
    }
  }
}
```

驗證採用 OAuth 2.0，支援動態探索和 Dynamic Client Registration。Cursor 會自行註冊，並在外掛連線時提示 Workable 登入，無需設定 API 金鑰。

<a id="before-you-connect"></a>

## 連線前注意事項

MCP 已包含於每個 Workable 訂閱方案，無額外費用，且不需要管理員角色，存取範圍僅限於登入使用者角色的許可權。

Workable 的 MCP OAuth 會檢查客戶端的 redirect URI 是否符合預核准列表。Workable 將 Cursor 列為支援的客戶端，但如果登入失敗並顯示 **redirect URI mismatch**，請聯絡 Workable Support 允許 Cursor 的兩個固定回呼：

| 介面 | Redirect URI |
|:--------|:-------------|
| Desktop | `http://localhost:8787/callback` |
| Web 和 Cloud Agents | `https://www.cursor.com/agents/mcp/oauth/callback` |

一個值得知道的特性：除了 `get_accounts` 之外，每個工具都需要 `account` 引數，因此代理必須先呼叫 `get_accounts`，並重用返回的子域名。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 能力 |
| --- | --- |
| 職位與 requisition | 建立和管理職位、職位貼文、requisition 和 pipeline 階段 |
| 候選人 | 搜尋、建立、評論、評分，並將候選人推進至不同階段 |
| Offer 與成員 | 管理 offer、團隊成員和帳戶設定 |
| 員工 | 員工記錄、部門、法人實體和工作排班 |
| 休假與追蹤 | 休假申請和餘額，以及時間追蹤記錄 |
| 績效 | 績效評估和評估週期 |

託管 runtime 是工具名稱和 schema 的來源依據。連線後，請呼叫 `get_accounts` 作為僅讀的 smoke test，因為每個其他工具都需要它的 `subdomain`。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Workable 使用者身份執行，且不能超出該使用者角色的許可權。
- 伺服器同時涵蓋 ATS **和** HRIS，因此可以寫入員工、休假、時間追蹤和績效資料，不只是招募相關資料。
- 傳輸採用無狀態的 streamable HTTP 於 JSON 模式，沒有 SSE endpoint。

<a id="docs"></a>

## 檔案

- Workable MCP server: https://workable.readme.io/reference/workable-mcp-server
- 使用 Workable MCP server: https://help.workable.com/hc/en-us/articles/39415735712407-Using-the-Workable-MCP-server-to-connect-to-AI-assistant
- Server URL: https://mcp.workable.com/mcp

Logo 是 Workable 的官方標誌，來自 `Workable` GitHub 組織。

<a id="license"></a>

## 授權

MIT
