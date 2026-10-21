# Daloopa

Cursor 外掛，透過 Daloopa 官方遠端 [Model Context Protocol](https://modelcontextprotocol.io/) 伺服器，將代理連線到 [Daloopa](https://docs.daloopa.com/docs/daloopa-mcp)。

取得來源連結的基本面資料、關鍵績效指標、申報檔案與價格。

<a id="install"></a>

## 安裝

1. 開啟 **Cursor 設定 → 外掛**。
2. 搜尋 **Daloopa**。
3. 點選 **安裝**，然後完成 Daloopa 登入提示。

或在聊天中執行 `/add-plugin daloopa`。

## MCP

```json
{
  "mcpServers": {
    "daloopa": {
      "type": "http",
      "url": "https://mcp.daloopa.com/server/mcp"
    }
  }
}
```

驗證方式為 OAuth。Cursor 會在外掛連線時提示 Daloopa 登入，不需要設定客戶端 ID 或個人存取令牌。

<a id="before-you-connect"></a>

## 連線前

你需要 Daloopa 帳戶與訂閱。涵蓋範圍為 6,000 多個全球股票程式碼，每個資料點都會連結回來源申報檔案、簡報或逐字稿。

<a id="what-agents-can-do"></a>

## 代理可執行的工作

| 分類 | 功能 |
| --- | --- |
| 公司 | 依股票程式碼或名稱尋找公司，並探索其可用系列 |
| 基本面資料 | 季度與年度財務報表、部門與關鍵績效指標的資料，附來源連結 |
| 檔案 | 列出、搜尋與閱讀申報檔案、逐字稿與簡報 |
| 價格 | 每日開高低收成交量歷史資料 |
| 技能 | 執行 Daloopa 預建的分析，例如盈餘檢視與比較分析 |

託管執行環境是工具名稱與結構的來源基準。

<a id="notes"></a>

## 注意事項

- 工具呼叫會在授權連線的使用者的 Daloopa 許可權範圍內執行。
- 位於 `https://docs.daloopa.com/mcp` 的獨立 docs 伺服器會搜尋 Daloopa 的說明檔案，並非此外掛所連線的伺服器。

<a id="docs"></a>

## 說明檔案

- Daloopa MCP: https://docs.daloopa.com/docs/daloopa-mcp
- 客戶端整合: https://docs.daloopa.com/docs/mcp-integrations
- 伺服器 URL: https://mcp.daloopa.com/server/mcp

標誌為 Daloopa 官方標誌，來自 `daloopa` GitHub 組織。

<a id="license"></a>

## 授權

MIT
