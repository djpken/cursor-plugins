# Typeform

Cursor 外掛，透過 Typeform 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Typeform](https://www.typeform.com)。

在已登入的 Typeform 帳戶中，建立和編輯表單、探索回應洞察，以及管理聯絡人和工作區。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Typeform**。
3. 點選 **安裝**，然後完成 Typeform 登入提示。

或在聊天中執行 `/add-plugin typeform`。

## MCP

```json
{
  "mcpServers": {
    "typeform": {
      "type": "http",
      "url": "https://api.typeform.com/mcp"
    }
  }
}
```

驗證採用 OAuth。外掛連線時，Cursor 會提示 Typeform 登入。個人存取令牌會被 MCP 伺服器明確拒絕，因此 OAuth 是唯一的路徑。

<a id="before-you-connect"></a>

## 連線前

此外掛指向 Typeform 的預設資料中心。如果你的帳戶位於 EU，請根據帳戶所在位置將伺服器 URL 更改為 `https://api.eu.typeform.com/mcp` 或 `https://api.typeform.eu/mcp`。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 表單 | 列出、讀取和建立表單，並檢查表單功能 |
| 洞察 | 探索和分析回應資料 |
| 聯絡人 | 列出聯絡人並透過對映匯入表單回應 |
| 工作區與帳戶 | 列出工作區和帳戶 |

託管執行環境是工具名稱和結構的來源。連線後，呼叫 `accounts-list_accounts` 作為僅讀的冒煙測試。

<a id="notes"></a>

## 注意事項

- 工具呼叫以授權連線的 Typeform 使用者身份執行。請求的範圍包括 `accounts:read`、`forms:read`、`forms:write`、`contacts:read`、`contacts:write`、`insights:read` 和 `workspaces:read`。
- Typeform 將其描述為功能有限的普遍可用 Beta 版本，因此工具目錄可能會變動。
- Streamable HTTP 是唯一支援的傳輸方式，沒有 SSE 端點。
- 如果剛授權後連線顯示沒有工具，請重新整理工具列表；Typeform 將此記錄為已知問題。

<a id="docs"></a>

## 文件

- Typeform MCP 伺服器：https://developers.typeform.com/developers/get-started/mcp/
- 將 Typeform 連線至你的 AI：https://help.typeform.com/hc/en-us/articles/50533862636308-Connect-Typeform-to-your-AI-with-the-Typeform-MCP-server
- 伺服器 URL：https://api.typeform.com/mcp

標誌是 Typeform 的官方標記，來自 `Typeform` GitHub 組織。

<a id="license"></a>

## 授權

MIT
