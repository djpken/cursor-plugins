# Interactive Brokers

Cursor 外掛，透過 Interactive Brokers 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Interactive Brokers](https://www.interactivebrokers.com/en/trading/ai-integrations.php)。

檢視部位、餘額、損益，並起草交易指令。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Interactive Brokers**。
3. 點選 **安裝**，然後完成 Interactive Brokers 登入提示。

或在聊天中執行 `/add-plugin interactive-brokers`。

## MCP

```json
{
  "mcpServers": {
    "interactive-brokers": {
      "type": "http",
      "url": "https://api.ibkr.com/v1/api/mcp-public"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 Interactive Brokers 登入，不需要設定客戶 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

您需要一個 Interactive Brokers 帳戶。登入時您會授權一個特定帳戶。此服務不適用於印度或日本的客戶。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 帳戶 | 餘額、摘要、部位與投資組合設定 |
| 活動 | 訂單、交易與歷史交易紀錄 |
| 市場資料 | 價格快照與歷史資料；合約搜尋 |
| 指令 | 起草交易指令，供您在 IBKR 的 AI 指令頁中檢視 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 交易指令不會自動變成實際訂單；您需在 IBKR 平臺內檢視並提交。
- IBKR 建議自訂客戶端使用 `mcp-public` 端點；Grok 的目錄列出 `/mcp`，兩者共用相同的授權伺服器。
- 從 **客戶端入口 → 設定 → 管理第三方同意** 撤銷存取權。

<a id="docs"></a>

## 檔案

- IBKR AI 整合：https://www.interactivebrokers.com/en/trading/ai-integrations.php
- 伺服器 URL：https://api.ibkr.com/v1/api/mcp-public

標誌為 Interactive Brokers 官方標記。

<a id="license"></a>

## 授權

MIT
