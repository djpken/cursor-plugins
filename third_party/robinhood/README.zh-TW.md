# Robinhood

Cursor 外掛，透過 Robinhood 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器（Robinhood Trading MCP）將代理連線到 [Robinhood](https://robinhood.com/us/en/support/articles/agentic-trading-overview/)。

檢視投資組合、持倉、訂單、觀察列表和市場資料，並在 Robinhood Agentic 帳戶中進行交易。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Robinhood**。
3. 點選 **安裝**，然後完成 Robinhood 登入提示。

或在聊天中執行 `/add-plugin robinhood`。

## MCP

```json
{
  "mcpServers": {
    "robinhood": {
      "type": "http",
      "url": "https://agent.robinhood.com/mcp/trading"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 Robinhood 登入，不需要設定客戶 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

你需要一個 Robinhood 帳戶。首次登入時，Robinhood 會提示你開設一個專用的 **Agentic** 帳戶；代理可以讀取你所有帳戶的資料，但只能在 Agentic 帳戶中進行交易，該帳戶需另行資金。開設 Agentic 帳戶和驗證代理必須在桌面瀏覽器中完成。

加密貨幣交易額需 Robinhood Crypto 帳戶，且並非在所有州（包括紐約）可用。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 帳戶 | 帳戶列表、投資組合快照、購買力、已實現損益和交易歷史 |
| 股票 | 持倉、稅務批次、報價、訂單歷史、可交易檢查，以及檢視/下單/取消訂單 |
| 期權 | 鏈、工具、報價、歷史資料、持倉、訂單歷史，以及檢視/下單/取消訂單 |
| 加密貨幣 | 貨幣對、報價、持倉、訂單歷史，以及預覽/下單/取消訂單 |
| 市場資料 | 歷史柱狀圖、基本面、財務資料、第二層價格簿、技術指標、盈利和指數 |
| 觀察列表 | 檢視、建立、更新、追蹤和管理股票、加密貨幣、指數和期權觀察列表 |
| 掃描器 | 建立、設定和執行股票掃描 |

託管執行環境是工具名稱和結構的來源。

<a id="notes"></a>

## 注意事項

- **你對代理所下的每一筆交易都有責任。** 如果你指示代理在未要求批准的情況下行動，它可以在無確認時下單。使用 `review_*` / `preview_*` 工具在下單前檢視交易前警告。
- 交易僅限於 Agentic 帳戶中的長期股票、期權和加密貨幣；Agentic 帳戶不支援保證金借款。
- Robinhood 不控制或審核第三方代理。連線前請檢視下方文件中的 Robinhood 披露。

<a id="docs"></a>

## 文件

- Agentic Trading 概述：https://robinhood.com/us/en/support/articles/agentic-trading-overview/
- 與你的代理交易（工具列表）：https://robinhood.com/us/en/support/articles/trading-with-your-agent/
- 伺服器 URL：https://agent.robinhood.com/mcp/trading

標誌是 Robinhood 的官方標記。

<a id="license"></a>

## 授權

MIT
