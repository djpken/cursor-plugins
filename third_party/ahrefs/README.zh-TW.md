# Ahrefs

Cursor 外掛，透過 Ahrefs 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Ahrefs](https://ahrefs.com)。

從已登入的 Ahrefs 帳戶查詢最新的 Ahrefs SEO 資料：反向連結、關鍵字指標、排名追蹤與網站稽核。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Ahrefs**。
3. 點選 **安裝**，然後完成 Ahrefs 登入提示。

或在聊天中執行 `/add-plugin ahrefs`。

## MCP

```json
{
  "mcpServers": {
    "ahrefs": {
      "type": "http",
      "url": "https://api.ahrefs.com/mcp/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Ahrefs 登入，同意畫面會在 Ahrefs 端產生 MCP 範圍的金鑰。請將任何 OAuth 客戶端 ID 與金鑰留為空白。

<a id="before-you-connect"></a>

## 連線前

您需要付費的 Ahrefs 方案（Lite 或更高）。MCP 呼叫會消耗方案的 Integration API 單位，每個回應的資料行上限會隨等級調整：Lite 為 100，Standard 為 250，Advanced 為 500，Enterprise 為無限制。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| Site Explorer | 反向連結資料、來源域名、自然流量與熱門頁面 |
| Keywords Explorer | 關鍵字指標、相關詞彙與 SERP 概覽 |
| Rank Tracker | 追蹤關鍵字排名與長期可見度 |
| Site Audit | 爬取結果與技術 SEO 問題 |

託管執行環境是工具名稱與結構的來源真相。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Ahrefs 使用者身份執行，並消耗該帳戶的 API 單位。
- Ahrefs MCP 金鑰與 API v3 金鑰是分開的，無法互相替代。OAuth 同意畫面會自動產生 MCP 範圍的金鑰；您也可以在 **帳戶設定 → API 金鑰 → 產生 MCP 金鑰** 下自行產生，並以 `"headers": {"Authorization": "Bearer <MCP_KEY>"}` 傳送來跳過 OAuth。
- `@ahrefs/mcp` npm 套件是 Ahrefs 舊的本地伺服器，Ahrefs 已不再支援本地設定：「本地 MCP 設定不再支援。我們目前僅支援遠端 MCP 伺服器。」

<a id="docs"></a>

## 文件

- Ahrefs MCP：https://ahrefs.com/mcp
- Ahrefs MCP 入門指南：https://help.ahrefs.com/en/articles/13913559-getting-started-with-ahrefs-mcp
- Ahrefs 開發者資源：https://docs.ahrefs.com/
- 連線設定（Copilot Studio）：https://docs.ahrefs.com/mcp/docs/copilot-studio
- 伺服器 URL：https://api.ahrefs.com/mcp/mcp

標誌是 Ahrefs 的官方標誌，來自 `ahrefs` GitHub 組織。

<a id="license"></a>

## 授權

MIT
