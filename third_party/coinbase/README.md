# Coinbase

Cursor 外掛，透過 Coinbase 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器將代理連線到 [Coinbase](https://docs.cdp.coinbase.com/coinbase-for-agents/overview)。

檢查餘額、取得報價，以及預覽或下單交易。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Coinbase**。
3. 點選 **安裝**，然後完成 Coinbase 登入提示。

或在聊天中執行 `/add-plugin coinbase`。

## MCP

```json
{
  "mcpServers": {
    "coinbase": {
      "type": "http",
      "url": "https://agents.coinbase.com/mcp"
    }
  }
}
```

驗證方式為 OAuth。當外掛連線時，Cursor 會提示 Coinbase 登入。不需要設定客戶端 ID 或 API 金鑰。每個使用者以自己的 Coinbase 帳戶登入，因此工具呼叫會針對該帳戶執行。Coinbase 建議將代理限定於專用的投資組合。

<a id="availability"></a>

## 可用性

Coinbase 僅將其遠端 MCP 伺服器提供給 [明確允許列表上的 harnesses](https://docs.cdp.coinbase.com/ai-agents/coinbase-for-agents/coinbase-mcp)。Cursor 尚未列入該列表，因此登入提示無法完成，直到 Coinbase 將 Cursor 加入。在此之前，請使用 Coinbase 的 [CLI 設定](https://docs.cdp.coinbase.com/ai-agents/coinbase-for-agents/coinbase-mcp#cli)（`@coinbase/coinbase-cli` 搭配 CDP API 金鑰）取代此外掛。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 類別 | 功能 |
| --- | --- |
| 市場資料 | 產品、報價、訂單簿與蠟燭圖 |
| 訂單 | 預覽、建立、編輯、取消與列出訂單；關閉部位 |
| 投資組合 | 餘額、投資組合細項與跨投資組合轉帳 |
| 轉換 | 報價與執行 USDC/USD 轉換 |

託管 runtime 是工具名稱與 schema 的來源真相。

<a id="notes"></a>

## 注意事項

- 透過此伺服器下單為實際交易。請先預覽，並將代理保持在獨立投資組閤中。
- 這是 Coinbase for Agents，並非獨立的 Agentic Wallet / x402 付款 MCP。

<a id="docs"></a>

## 文件

- Coinbase for Agents: https://docs.cdp.coinbase.com/coinbase-for-agents/overview
- 伺服器 URL: https://agents.coinbase.com/mcp

Logo 是 Coinbase 官方標誌，來自 `coinbase` GitHub 組織。

<a id="license"></a>

## 授權

MIT
