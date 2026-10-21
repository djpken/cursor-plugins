<a id="profound"></a>

# 深奧

Cursor 外掛，透過 Profound 官方託管的 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Profound](https://www.tryprofound.com)。

取得 AI 可見度、情感與引用報告，存取代理分析，並在已登入的 Profound 帳戶中建立或執行 Profound Agents。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Profound**。
3. 點選 **安裝**，然後完成 Profound 登入提示。

或在聊天中執行 `/add-plugin profound`。

## MCP

```json
{
  "mcpServers": {
    "profound": {
      "type": "http",
      "url": "https://mcp.tryprofound.com/mcp"
    }
  }
}
```

驗證是透過 OAuth 2.1 對 Profound 進行。當外掛連線時，Cursor 會提示 Profound 登入，不需要設定 API 金鑰或客戶 ID。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 探索 | 確認已登入的使用者，然後列出組織、區域、模型、類別、領域、標籤、主題與提示 |
| 可見度 | 在 ChatGPT、Perplexity、Gemini 與 Google AI Overviews 等答案引擎中的聲量佔比、提及與排名 |
| 引用與情感 | 追蹤答案引擎引用哪些頁面，以及它們如何討論品牌或類別 |
| 代理分析 | 爬蟲報告、來自答案引擎的推薦流量與原始分析 |
| Profound Agents | 列出、建立、發布與執行 Agents，然後監控執行狀態與輸出 |

託管執行環境是工具名稱與結構的來源。連線後，將 `whoami` 作為僅讀的冒煙測試呼叫。

<a id="notes"></a>

## 注意事項

- 工具呼叫會以授權連線的 Profound 使用者身份執行，且不能超過該使用者許可權。
- 分析工具僅讀。Agent 工具可以在已登入的組織中建立、更新、發布與執行 Agent 定義。
- 企業方案可以使用長期有效的 Bearer API 金鑰進行驗證，而非 OAuth。此外掛使用建議的 OAuth 流程。請參閱 [Authentication](https://docs.tryprofound.com/mcp/authentication)。

<a id="docs"></a>

## 文件

- Profound MCP 概述：https://docs.tryprofound.com/mcp/overview
- 連線 AI 編碼工具：https://docs.tryprofound.com/mcp/connect
- 分析功能：https://docs.tryprofound.com/mcp/capabilities/analytics-capabilities
- Agents 功能：https://docs.tryprofound.com/mcp/capabilities/agents-capabilities
- 伺服器 URL：https://mcp.tryprofound.com/mcp

標誌是 Profound 官方 GitHub 組織標記（黑色背景上的白色同構圖）。

<a id="license"></a>

## 授權

MIT
