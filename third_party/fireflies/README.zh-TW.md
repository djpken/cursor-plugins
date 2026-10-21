# Fireflies

Cursor 外掛，透過 Fireflies 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Fireflies](https://fireflies.ai)。

搜尋並取得 Fireflies 會議記錄、摘要、行動專案與精彩片段，並管理會議分享與頻道。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Fireflies**。
3. 點選 **安裝**，然後完成 Fireflies 登入提示。

或在聊天中執行 `/add-plugin fireflies`。

## MCP

```json
{
  "mcpServers": {
    "fireflies": {
      "type": "http",
      "url": "https://api.fireflies.ai/mcp"
    }
  }
}
```

驗證是透過 OAuth 對您的 Fireflies、Google 或 Microsoft 帳戶進行。當外掛連線時，Cursor 會提示登入。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 會議 | 搜尋會議，然後取得記錄、摘要與行動專案 |
| 管理 | 分享、撤銷、重新命名與移動會議 |
| 精彩片段與頻道 | 處理精彩片段與會議頻道 |
| 團隊 | 使用者與團隊分析 |
| 自動化 | 讀取自動化規則執行記錄（企業版） |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Fireflies 使用者身份執行。
- Fireflies 也接受 API 金鑰，以 `Authorization: Bearer <API_KEY>` 傳送，可在 **設定 → 開發者設定** 下產生。
- `fireflies_get_rule_executions` 需要企業版存取權，而 `fireflies_search` / `fireflies_fetch` 為實驗性功能，仍在逐步推出。
- Fireflies 的 Zero-Day Retention 政策不涵蓋透過 MCP 離開的資料。
- Fireflies 執行另一個獨立 MCP 伺服器，用於文件搜尋，位於 `https://docs.fireflies.ai/mcp`。此外掛指向會議伺服器。

<a id="docs"></a>

## 文件

- Fireflies MCP 設定：https://docs.fireflies.ai/getting-started/mcp-configuration
- MCP 工具概覽：https://docs.fireflies.ai/mcp-tools/overview
- 伺服器 URL：https://api.fireflies.ai/mcp

Logo 是 Fireflies 的官方標誌，來自 `firefliesai` GitHub 組織。

<a id="license"></a>

## 授權

MIT
